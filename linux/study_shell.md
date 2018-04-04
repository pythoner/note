```
source ~/openrc

get_token(){
    token_url=`printf "%s%s" $OS_AUTH_URL "tokens"`
    curl -X POST $token_url -d '{"auth":{"passwordCredentials": {"username": '\"$OS_USERNAME\"', "password": '\"$OS_PASSWORD\"'}, "tenantName":'\"$OS_TENANT_NAME\"'}}' -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json">token.txt
    token=`python -c 'import json, sys;response=json.load(open("token.txt"));print response.get("access", {}).get("token", {}).get("id", "")'`
    if [ "$token" = '' ]; then
        echo "0"
    else
        echo "${token}"
    fi
}




get_nova_endpoint(){
    token_url=`printf "%s%s" $OS_AUTH_URL "tokens"`
    curl -X POST $token_url -d '{"auth":{"passwordCredentials": {"username": '\"$OS_USERNAME\"', "password": '\"$OS_PASSWORD\"'}, "tenantName":'\"$OS_TENANT_NAME\"'}}' -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json">token.txt
    endpoint=`python -c 'import json, sys;response=json.load(open("token.txt"));print [item["endpoints"][0].get("publicURL") for item in response.get("access", {}).get("serviceCatalog", []) if item.get("type") == "compute"][0]'`
    if [ "$endpoint" = '' ]; then
        echo "0"
    else
        echo "${endpoint}"
    fi
}

create_server(){
   token=`get_token`
   endpoint=`get_nova_endpoint`
   if [ "${token}" = "0" ]; then
      echo "Get token failed"
   fi
   endpoint=`get_nova_endpoint`
   nova_url=`printf "%s/%s" $endpoint "servers"`
   #curl -X POST http://api.pub.lenovo.com:8774/v2/bbb2d0d29f9042ab838c08900802ac21/servers -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: ${token}" -d '{"server": {"name": '\"$1\"', "imageRef": '\"$2\"', "flavorRef": '\"$3\"', "max_count": 1, "min_count": 1, "networks": [{"uuid": '\"$4\"'}]}}' | python -m json.tool
   curl -X POST $nova_url -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: ${token}" -d '{"server": {"name": '\"$1\"', "imageRef": '\"$2\"', "flavorRef": '\"$3\"', "max_count": 1, "min_count": 1, "networks": [{"uuid": '\"$4\"'}]}}' | python -m json.tool

}


stop_server(){
   token=`get_token`
   endpoint=`get_nova_endpoint`
   nova_url=`printf "%s/%s/%s/%s" $endpoint "servers" $1 "action"`
   curl -X POST $nova_url -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: ${token}" -d '{"os-stop": true}' | python -m json.tool
   #echo '{"server": {"name": '"$1"', "imageRef": '"${2}"', "flavorRef": '"$3"', "max_count": 1, "min_count": 1, "networks": [{"uuid": '"$4"'}]}}'

}


start_server(){
   token=`get_token`
   endpoint=`get_nova_endpoint`
   nova_url=`printf "%s/%s/%s/%s" $endpoint "servers" $1 "action"`
   curl -X POST $nova_url -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: ${token}" -d '{"os-start": true}' | python -m json.tool

}


case "$1" in
    create_server)
        shift
        create_server $@
        ;;
    stop_server)
        shift
        stop_server $@
        ;;
    start_server)
        shift
        start_server $@
        ;;
    *)
        echo $"Usage: $0 {create_server|stop_server|start_server}"
        echo $"samples $0 create_server name image_uuid flavorid network_uuid"
        echo $"samples $0 stop_server instance_uuid"
        echo $"samples $0 start_server instance_uuid"
        exit 2
esac
exit $?

```

第一次写shell， 总结一下几个问题

1. shell的返回值只能返回整数。  如果返回字符串， 且被其他函数所使用， 那么可以参考*get_token*
这个例子。
   调用get_token, 获取返回值， 使用
   ```
   token=`get_token`
   ```

2. 在我们post的时候， 有些data需要从中获取， 如果使用``的话， 就无法取道变量的值。可以参考*get_token*中的post使用
  ```
  # 注意'与"的使用， 感觉就是作字符串拼接
  curl -X POST $token_url -d '{"auth":{"passwordCredentials": {"username": '\"$OS_USERNAME\"', "password": '\"$OS_PASSWORD\"'}, "tenantName":'\"$OS_TENANT_NAME\"'}}' -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json"
  ```

3. 赋值与条件
   * 赋值"="前后**一定不要加空格**
   * 类似于if这样的， "[" "]" **一定加空格**

4. 对于json的解析目前我没有好办法， 回头看看吧。

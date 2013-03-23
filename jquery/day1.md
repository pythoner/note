* 在jQuery库中， $就是jQuery的一个简写形式，例如$("foo")和jQuery("#foo")是等价的， $.ajax和jQuery.ajax是等价的。
* 以下两种形式是等价的
	<pre>
	//形式1
	$(document).ready(function){
		// do something	
		};
	//形式2
	$(function(){

	});
	</pre>

*	windows.onload和$(document).ready()对比

	<html>
 	<head>
	<script type="text/javascript" src="../js/bootstrap.js"></script>
	<link rel="stylesheet" href="../css/main.css"></link>
	<link rel="stylesheet" href="../css/bootstrap.css"></link>
	<link rel="stylesheet" href="../css/bootstrap-responsive.css"></link>
	</head>	
	<body>
		<table class="table">
			<tr><th></th>
				<th>window.onload()</th>
				<th>$(document).ready()</th>
			</tr>
			<tr><td>执行时机</td>
				<td>必须等待网页中所有的内容加载完毕后(包括图片)才能执行</td>
				<td>网页中所有的DOM加载完毕</td>
			</tr>
			<tr>
				<td>编写个数</td>
				<td>可以写多个，但是最后一个会把前面的全部覆盖
						windonw.onload = function(){
							alert("test1");
							}<br /><br />
						windonw.onload = function(){
							alert("test2");
							}
						这里只会输出test2。
				</td>
				<td>$(function(){	<br />
							alert("test3");<br />
							});<br />
						$(function(){<br />
							alert("test4");<br />
						});<br />
				    这里的test3和test4都会显示。
					</td>
			</tr>
			<tr>
				<td>简化写法</td>
				<td>无</td>
				<td>
					$(function(){ <br />
					});
				</td>
		</tr>
	</table>
	</body>
	</html>


* jQuery链式写法 ---ex2-navigation.html
	
	<pre>
		$(function(){
			$(".has_children").click(function(){
				//为当前元素增加高亮类
				$(this).addClass("hightlight") 
		//将子节点中的链接全部显示，使用end重新定位到上次操作的元素, 这里即$(this)
					.children("a").show().end()   
					 //兄弟元素去掉高亮
					.siblings().removeClass(".hightlight") 
					//子元素隐藏	
						   .children("a").hide();          			
			});
		});
	</pre>

* jQuery对象和DOM对象互转

	jQuery是通过jQuery包装DOM后产生的对象， jQuery对象是jQuery独有的，如果一个对象是jQuery对象， 那么他就可以使用jQuery里面的方法。
	+ DOM对象转jQuery对象
		
	对于一个DOM对象， 只需要用$将DOM对象包装起来，那么DOM对象就转换为了jQuery对象

	+ jQuery对象转DOM对象
		
	jQuery对象提供了两种方法将一个jQuery对象转换为DOM对象， 即[index]和get(index)
	
		- jQuery对象是一个对象数组， 可以通过[index]的方式获取相应的DOM对象
			var $cr = $("#cr")
			var cr = $cr[0]

		- 另外一种方法，是jQuery自身提供的，通过get(index)获取相应的DOM对象
			var $cr = $("#cr")
			var cr = $cr.get(0)
		
	+ 平时用到的jQuery对象都是通过$()制造出来的，$()就是一个jQuery对象的制造工厂


* is(":checked") 判断jQuery对象是否选中
	
	$("checkboxid").is(":checked")


* 解决jQuery库和其他库的冲突

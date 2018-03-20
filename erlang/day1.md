### days

* Erlang Run-Time System Application (ERTS)


[原文](http://erlang.org/doc/reference_manual/data_types.html)


### Built-In Functions (BIFs)


#### Expressions 表达式
#### Data Types
* Terms

  A piece of data of any data type is called a term.

* Number

  有两种类型的数字的字面量。 **Intergers** 和 **floats**. 同时还有两种转换的标记

  * $char

    字符char的ascii值或者unicode 对应的code point.

  * base#value

    base进值的整数值。base需要在2..36之间。

  examples
  ```
  1> 42.
  42
  2> $A
  2> .
  65
  3> 2#101.
  5
  4> 16#1f.
  31
  5> 2.3.
  2.3
  6> 2.3e3.
  2.3e3
  7> 2.3e-3.
  0.0023
  8> $\n.
  10
  ```

* Atom

  atom是一个字面的常量. 如果atom不是以小写字母开头或者包含非**数字字母**， **_** 和 **@** 之外的其他字符， 则用单引号''括起来。

  examples:

  ```
  9> hello.
  hello
  10> phone_number.
  phone_number
  11> "Monday".
  "Monday"
  12> "phone number".
  "phone number"
  13> phone number   
  13> .
  * 1: syntax error before: number
  13> Monday.
  * 1: variable 'Monday' is unbound
  ```

* Bit strings 和binaries

    * bit string 用于存储隐式内存的区域。

    * bit string有一系列的bit组成。 八个bit称之为binaries。

    examples

    ```
    1> <<10, 20>>.
  <<10,20>>
  2> <<"ABC">>.
  <<"ABC">>
  3> <<1:1, 0:1>>.
  <<2:2>>

    ```

* Reference
* Fun
* Port Identifier
* Pid
* Tuple

  tuple是固定数量term的组合数据类型

  examples

  ```
  {Term1,...,TermN}
  ```

  元组中的每个term称之为元素。 term的数量称之为元组的大小。

  ```

  1> P = {adam, 24, {july, 29}}.
  {adam,24,{july,29}}
  2> element(1, P).
  adam
  3> element(3, P).
  {july,29}
  4> P2 = setelement(2, P, 25).
  {adam,25,{july,29}}
  5> tuple_size(P).
  3
  6> tuple_size({}).
  0
  ```

* Map

  Map是一个key-value的键值组合。

  ```
  #{Key1=>Value1,...,KeyN=>ValueN}
  ```
  examples
  ```
  1> M1 = #{name=>adam,age=>24,date=>{july,29}}.
  #{age => 24,date => {july,29},name => adam}
  2> maps:get(name,M1).
  adam
  3> maps:get(date,M1).
  {july,29}
  4> M2 = maps:update(age,25,M1).
  #{age => 25,date => {july,29},name => adam}
  5> map_size(M).
  3
  6> map_size(#{}).
  0
  ```

* List

  ```
  [Term1,...,TermN]
  ```

* string
* Record
* Boolean

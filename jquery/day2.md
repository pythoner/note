**jQuery选择器**

选择器是jQuery的基础， 在jQuery中，对事件处理， 遍历DOM和Ajax操作都依赖于选择器。 如果能够熟练的使用选择器， 不仅能简化代码， 而且还可以达到事半功倍的效果

+ 在了解jQuery选择器前，先了解一下css选择器， 因为jQuery选择器，完全继承了css选择器的风格。常用的css选择器有以下几种：
	
	* 标签选择器 -- 以文档元素作为选择符号。在jQuery中的表现形式为:  $("Element"), 实例如下：
	* ID选择器  -- 以文档元素唯一的标识符ID作为选择器。 在jQuery中的表现形式为： $("#Element_ID")
	* 类选择器 -- 以文档元素的class作为选择器
	* 群组选择器
	* 后代选择器
	* 通配选择器
	

+ jQuery选择器的优点
	* 简洁的语法
		
		使用$()来作为选择器的函数

	* 支持css1到css3
	* 完善的处理机制
		
		jQuery获取网页上不存在的元素不会报错。 eg. 
		$("#tt").css("color", "red"), 如果ID为tt的元素不存在，那么对于jQuery程序也没有任何的影响，也不会有任何的错误信息。

+ jQuery选择器分类

	jQuery选择器分为基本选择器，层次选择器，过滤选择器， 和表单选择器
	* 基本选择器
		基本选择其是jQuery中最常用的选择器， 他通过ID，class， 标签等来查找DOM元，下面是列出的基本选择其

	<html>
 		<head>
		<script type="text/javascript" src="../js/bootstrap.js"></script>
		<link rel="stylesheet" href="../css/bootstrap.css"></link>
		<link rel="stylesheet" href="../css/bootstrap-responsive.css"></link>
	</head>	
	<body>
	<table class="table">
		<tr>
			<th>选择器</th>
			<th>描述</th>
			<th>返回</th>
			<th>示例</th>
		</tr>
		<tr><td>ID选择器 (#id)</td>
			<td>根据给定的ID匹配一个元素</td>
			<td>单个元素</td>
			<td>$("#tt")选取id为tt的元素</td>
		</tr>
		<tr>
			<td>类选择器 (.class)</td>
			<td>根据给定的类名匹配元素</td>
			<td>集合元素</td>
			<td>$(".test")选取class为test的所有元素</td>
		</tr>
		<tr>
				<td>元素选择器</td>
				<td>根据给定的元素名称返回匹配的元素</td>
				<td>集合元素</td>
				<td>$("span")返回所有的span元素</td>
		</tr>
		<tr>
				<td>通配选择器</td>
				<td>匹配所有元素</td>
				<td>集合元素</td>
				<td>$("*")返回所有的元素</td>
		</tr>
		<tr>
				<td>群组选择器 (selector1, selector2,...)</td>
				<td>将每一个选择器匹配到的元素合并后一起返回</td>
				<td>集合元素</td>
				<td>$("span, div, #one")选取所有的span， div，和id为one的选择</td>
		</tr>
	</table>
	</body>
	</html>

	举例见ex4-selector.html

	* 层次选择器
		
		<html>
 		<head>
		<script type="text/javascript" src="../js/bootstrap.js"></script>
		<link rel="stylesheet" href="../css/bootstrap.css"></link>
		<link rel="stylesheet" href="../css/bootstrap-responsive.css"></link>
	</head>	
	<body>
	<table class="table">
		<tr>
			<th>选择器</th>
			<th>描述</th>
			<th>返回</th>
			<th>示例</th>
		</tr>
		<tr><td>$("ancestor descendant")</td>
			<td>选取ancestor后的所有descentdant元素</td>
			<td>集合元素</td>
			<td>$("div span")选取div后的所有span元素</td>
		</tr>
		<tr>
			<td>$("parent > child")</td>
			<td>选取子元素， 而$("ancestor descendant")选择的是所有后代元素</td>
			<td>集合元素</td>
			<td>$("div > span")选取div下所有的span子元素</td>
		</tr>
		<tr>
				<td>$("prev + next")</td>
				<td>选取紧接prev后的next元素</td>
				<td>集合元素</td>
				<td>$(".one + div")选取class为one的下一个div元素。 与next()功能类似。</td>
		</tr>
		<tr>
				<td>$("prev ~ siblings")</td>
				<td>选取prev后的所有sibling兄弟元素</td>
				<td>集合元素</td>
				<td>$("#two ~ div")选取id为two后的所有div兄弟元素。 <span style="color:red;">需要说明的是， 这里选择的是prev之后的兄弟元素， 而不是所有的兄弟元素，这里的功能与nextAll()类似，而siblings()是选取所有的兄弟元素，不论前后.</span></td>
		</tr>
		
	</table>
	</body>
	</html>

	举例见ex5-selector.html
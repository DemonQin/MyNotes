一·HTML基本格式

	<!--注释-->
	1.<!DOCTYPE html> html的文档声明,告诉浏览器html的版本
	2.html中的内容分为2部门,head中的内容是给浏览器看的设置,title是唯一一个可以被用户看到的,body中的内容是给用户看的
	3.<meta charset="UTF-8"> 告诉浏览器以什么样的编码格式来解析页面的内容
编码:文件的编码格式
解码:<meta charset="UTF-8">
编码和解码不统一会出现乱码,默认gbk
	4.标签:
双标签|闭合标签: <title>Title</title>
单标签|自闭合标签: <meta charset="UTF-8">
	5.属性:更完整的展示标签的功能
标签可以添加多个属性
属性名='属性值'  可以需要添加引号,单引号双引号都行
属性前面要添加空格

二·标签
标签的分类:
行内元素: 
	元素的大小由内容撑起来,多个元素可以一行显示
	不能设置宽高
	只能嵌套行内元素或文本
块元素:
	自己独占一行,不能和其他元素一行显示
	可以设置宽高,独占一行
	可以嵌套行内元素,嵌套其他块元素,文本
	属性:
align 对齐方式
left 左对齐  right 右对齐  center居中对齐

a 超链接标签
	href 必填属性 定义连接路径
1.	绝对路径
2.	相对路径
3.	没有地址  每点击以此刷新一次
4.	#回到页面顶部但是不刷新
	target 打开方式
1.	_self 本页面打开  默认
2.	_blank 新页面打开
	title 当鼠标悬停在内容上时候显示的提示字
	特点:
1.	自带下划线
2.	连接未访问:字体蓝色
3.	连接已访问:字体紫色

	锚点|锚链接的作用
p 段落标签
1.	定义段落内容
2.	块元素
3.	只能嵌套行内元素和文本
div 块
块元素
span 设置样式
本身没有任何作用的行元素

h1~6 标题标签
1.	根据权重不同,字体大小以此减小
2.	加粗
3.	块元素
img 图像标签
	src 图片的地址
1.	绝对地址
2.	相对地址
	alt 当图片无法正常显示的时候,显示提示字
	title 当鼠标悬停在内容上时显示的提示字

	html提供的能改变样式的属性
1.	width 宽度   px为单位 是网页开发最基本的单位
2.	eight 高度
3.	border 边框
	align: 对其方式
1.	left 对其到左边
2.	right 对其到右边
3.	垂直方向的对其方式
4.	top 顶部对其
5.	bottom 底部对其
6.	center 居中对其

	路径:
1.	进入某个路径下   文件夹名字/
2.	回到上一层目录   ../
列表:
	ul,ol添加一个type属性修改列表项标记
	列表中的内容必须定义在列表项li中
	无序列表
1.	ul  无序列表标签
2.	li  列表项
3.	type:circle 空心圆圈 square实心方块  desc实心圆圈
1.	有序列表
1.	ol  有序列表标签
2.	li  列表项
3.	type: 1  A  a  I  i

2.	css样式表: 标签上添加style属性->css样式   width:100px;height:100px;list-style:none;去除列表项标记



表格 : 展示数据的一种手段


3.	table 定义表格

1.	tr    行
2.	th    表格头单元格  加粗居中
3.	td    表格体单元格
4.	先有行再有列  内容必须存在与单元格中



4.	属性

1.	border 变框
2.	style="border-collapse: collapse" 双线变单线
3.	width 宽度
4.	height 高度
5.	align  对其方式
6.	bordercolor 变框颜色
7.	rowspan 跨行
8.	colspan 跨列

学习html: 学习不同的标签,作用,特点,属性,属性值

        表单:收集数据   *****
form 表单标签
5.	属性
1.	name : 表单的名字
2.	action : 数据提交的地址
3.	method : 提交方式  get  post
4.	enctype : 当上传文件的时候需要修改特殊的值multipart/form-data,否则默认


6.	表单元素:form标签内部的元素
1.	input 标签定义大部分的表单元素
a)	type属性的值确定不同的表单元素
b)	text 普通文本框  name value
c)	password 密码框  加密
d)	radio 单选框
一组只能选择一个,name属性值相同就是一组
e)	checkbox 多选框  一组
f)	hidden 隐藏框
g)	button 按钮  常常结合js使用
h)	submit 提交
i)	reset  重置

1.	select 下拉列表框
option 下拉列表选项
2.	提交:
input-> submit 提交
3.	textarea 多行文本域 使用样式控制大小

fieldset

4.	id 和  name  和 class 和 value
id 区分一个页面所有元素的唯一  js使用的多
class 结合css使用的多
name  能够提交数据的表单元素必有的属性,后台区分的唯一
value 表单元素的值->手机的数据

5.	常用的属性:
autofocus 自动聚焦
checked selected 默认选中
placeholder 提示字
disabled 禁用  不能修改,不能提交
readonly 只读  不能修改,可以提交
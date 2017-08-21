---
layout: post
title: CSS3系统学习笔记
categories: StudyNotes
description: 历次出游摄影的一些失败的点。
keywords: CSS3
---

CSS概述
	1、用于HTML文档中元素的样式定义
	2、尽量用CSS样式取代html属性
	3、使用CSS样式的方式：
		内联方式：定义在html元素中，不利于重用；
		内部样式：html页面的头元素中，放在一对style标记里；
		外部样式：将样式定义在外部CSS文件中，由html页面引用样式表文件；
			样式规则由选择器和样式声明组成。
				选择器:决定哪些元素使用这些规则；
				样式声明：一对大括号，包含一个或多个属性/值对
CSS特征
	继承性、层叠性（多个样式层叠为一个）、优先级

CSS样式优先级
	1、由低到高：浏览器缺省设置-》外部、内部样式（子元素高于父元素）-》内联样式；最后一次定义为准。
	2、改变最高优先级：属性值后增加 ！important
	3、html自带样式的优先级最低
CSS选择器
	1、基础选择器
		1、通用选择器，与任何元素匹配，慎用，会降低效率 *{}
		2、元素选择器 html{} h1{}等
		3、类选择器 .className{color:red;},不能以数字开头；
			1、配合元素选择器实现对元素的细分设置。red类型对h1元素生效 h1.red{}
			2、多类选择器：可将多个类应用于同一个元素,类之间用空格分开 class="class1 class2"
		4、id选择器 #idName{},唯一性，一个元素只能有一个id，一个id名称只能出现一次。
		优先级：由低到高 元素-》class选择器-》id选择器
	2、复合选择器
		1、群组选择器:以逗号隔开的选择器列表，相同规则作用于多个元素
			例：h1,h2,.red{color:red;}
		2、后代选择器（包含选择器）：对父元素的所有该后代元素起作用
			例：h1 span{}
		3、子代选择器：对父元素中的直接子元素起作用
			例；h1>span{}
		4、伪类选择器：用于向某些选择器添加特殊效果，（-选择器：伪类选择器）
			1、链接伪类，用户定义链接-一般项目中不使用，直接定义a标记样式
				-:link,适用于尚未访问的链接
				-:visited,适用于访问过的链接,不支持定义字体大小
			2、动态伪类，用于呈现用户操作
				-:hover,适用于鼠标悬停在html元素时【低版本下不支持a以外的hover】
				-:active,适用于html元素被激活时
				-:focus,适用于html元素获得焦点时，只适用于表单元素
		选择器优先级:
			元素选择器：0,0,0,1
			类选择器：  0,0,1,0
			伪类选择器：0,0,1,1
			ID选择器：  0,1,0,0
			内联样式：  1,0,0,0
			权值大的优先，权值相同，靠后的优先。
		【样式不生效,考虑代码拼写和优先级】
		CSS优化（高级）：了解浏览器对CSS代码解释规则，能不用后代就不用，元素加类效率低
CSS尺寸与边框
单位
	1、尺寸单位 %:百分比 ; pt:磅 9pt=12px ;px:像素;em:2em当前字体尺寸的两倍；
	2、颜色单位：屏幕显示的颜色都是RGB:red、green、blue,色光三原色。
		表达方式：rgb(X,X,X);rgb(X%,X%,X%);十六进制rgb颜色值：#rrggbb 如#f2f2f2，如;#rgb;英文关键字
属性
	1、尺寸属性：长宽可变时，最大最小尺寸起作用。width:80%;min-width:600px;max-width:600px;
		height:300px;min-height:max-height:
		【注意：宽和高只对块元素生效，行内元素是不生效的，比如a i 标签】
	   溢出:内容所需空间大于框本身
		overflow:visible; 溢出可见
		overflow:hidden; 溢出隐藏
		overflow:scroll;始终显示滚动条
		overflow:auto; 自动判断
		overflow-x  overflow-y 两个需要同时设置，如果只设置其中一个为auto,另一个会跟随这个。
	   哪些元素可以设置尺寸属性：块级元素：-p,-div,-h1-h6,ul,ol,li,dl,dt,dd等，存在weith,height的html元素如img，
table，input
	2、边框属性：
		边框线：
			简单写法:border:width style color;
			单边定义：border-left/right/top/bottom:width style color;
			单独定义宽、样式、颜色
			border-left/right/top/bottom-width 隐藏边框： border-right:none;（高效）
								border-top-color:transparent;border-bottom:0px;
			border-left/right/top/bottom-style  ：solid实线 dashed虚线 dotted:点线
			border-left/right/top/bottom-color
		边框倒角： border-radius:50px 20px; 顺时针，未定义的角按对角线
		边框阴影：box-shadow:h-shadow   v-shadow   blur    spread color inset;
(必须)水平向右阴影(必须)垂直向下阴影 模糊距离 阴影尺寸 阴影颜色 外阴影改为内阴影
		box-shadow:5px 5px 5px 2px #ccc; 第四个值向外扩展可以省去，第一个和第二个值为0时是四周均匀扩展,
可为负数，阴影颜色与字体颜色一致。
		*图片边框：border-image:source width repeat;border-image-source/width/repeat、round、stretch(平铺、铺满
、拉伸)
			如： border-image:url(border.png) 26 repeat;
			图像九宫格切片处理，四个顶点是四个角，中间区域平铺。width是指切的四个角像素大小，不加单位
			ie8低版本浏览器不支持
		轮廓：是绘制于元素周围的一条线，位于边框边缘的外围，可起到突出元素的的作用。
			outline:color style width;
			outline-width:轮廓的宽度
			outline-style:轮廓的样式
			outline-color:轮廓的颜色
			outline:none;去掉chrome浏览器focus时的蓝边

框模型-盒子模型Box Model（必须掌握）：元素内容、边框、内边距、外边距
	元素框（左右）的总尺寸 = （左右）外边距margin + （左右）外边框border + （左右）内边距padding + 内容宽度width
	1、外边框 margin：围绕在元素周围的空白区域。
		四个值顺时针，margin:10px 20px;上下和左右，margin:10px 20px 30px;上右下 最后一个与对面相同
		1、取值：正、负、0、auto左右居中对齐
		2、margin决定上下左右的间距，并不能改变元素的布局方式
		3、当上下margin相遇时，取大值，不叠加。
		4、body是自带外边距，如需紧贴浏览器边，可设为0。
		5、块级元素div,h1，p等也自带外边距，可声明覆盖。
		6、行内元素只有左右margin生效。
	2、内边距 padding :
		1、内容与边框间的距离，会扩大元素边框所占区域
		2、取值范围：没有负值
背景
	1、背景色 background-color：transparent。去掉容器自带背景色
		背景色会填充元素的内容、内边距、边框区域。
	2、背景图
		background-image:url(url);
		background-repeat:no-repeat/repeat/repeat-x/repeat-y;
		*background-size:3px 4px/cover/contain;宽和高 CSS3新属性，有兼容问题
		*background-attachment:fixed;背景图固定属性
		background-position:left center; 20% 30%; 40px 50px 左下偏移，可取负
			将图拼在一起，可以减少访问量，减轻服务器负担，这种技术称为： CSS sprites css精灵，即把图片拼在一块。
			背景色和背景图同时存在，显示背景图
		*background-clip：规定背景颜色绘制区域 border-box,padding-box默认,content-box CSS3新属性，有兼容问题
		background-orign:背景定位区域
		简写：background:color url repeat attachment position
	3、背景渐变（CSS3属性，低版本ie不支持）
		两种或多种颜色的平滑过度，用background-image属性设置
		可取值
			- linear-gradient:线性渐变
			- radial-gradient:径向渐变
			- repeating-linear-gradient:重复线性渐变
			- repeating-radial-gradient:重复径向渐变
		1、线性渐变background-image: linear-gradient(angle,color-point1,color-point2,..)
			angle:渐变的方向，关键词或角度，
			如- to top(0deg)
			-to right(90deg)
			-to bottom(180deg)
			-to left(270deg)
			color-point:表示颜色的起点、中间点、或终点
			取值为颜色和位置组合,如 red 0%,green 20%,
			例：
			background-image:linear-gradient(180deg,yellow,red);
			background-image:linear-gradient(180deg,yellow 20%,red 40%,silver 60%);
			第二个渐变位置要大于第一个位置才有渐变，百分比表示渐变开始的位置。
		2、径向渐变 background-image:radial-gradient([size at position],color-point1,color-point2,..)
			position:圆心位置，默认为center,可取值：数值，百分比，关键字
			color-point:表示颜色的起点、中间点、或终点
			取值为颜色和位置组合,如 red 0%,green 20%,
			例：
			background-image:radial-gradient(100px at left bottom,yellow 0%,red 100%);
			background-image:radial-gradient(100px at 50px 100px,yellow 0%,red 100%);
			background-image:radial-gradient(at 50px 100px,yellow 0%,red 100%);
		3、重复渐变 ：一般不用，注意留下重复的空间，如 0%-20%
			background-image:repeating-linear-gradient(to top,yellow 0%,red 20%);
渐变的兼容性
	目前，各浏览器的新版本均支持渐变，对于不支持的版本,可在属性前加浏览器厂商前缀
	-Firefor   -moz-
	-Chrome和Safari  -webkit-
	-Opera   -o-
	例：#d4{
		background-image:linear-gradient(180deg,yellow,red);
		/*兼容火狐*/
		background-image:
		-moz-linear-gradient(180deg,yellow,red);
		/*兼容谷歌*/
		background-image:
		-webkit-linear-gradient(180deg,yellow,red);

		}

文本格式化
	主要包括
	-控制字体:如字体大小、字体加粗、字体系列等。
	-设置文本格式：如文本颜色、文本排列和文本缩进等。
	字体属性：
		1、指定字体
			例：
			font-family:Arial,"微软雅黑";
			/*font-family字体原则，如果是中文就用中文字体，如果是数字或英文，就用英文字体。*/
			/*多个逗号分开，从左到右优先显示：font-family:"微软雅黑","楷体","宋体";*/
			/*中英文同时存在，想让英文显示英文格式，中文显示中文格式，则英文放前面  font-family:Arial,"微软雅黑";*/
			/*Times类似宋体，Arial类似黑体*/

		补充：【处理上下不同字数两端对齐】
			font-family:simsun;
			*letter-spacing:2em;字距 如果上面不兼容，在字后面加这个。现在项目一般用右对齐。
			*word-spacing:词距
		2、字体大小
			例：
			font-size:14px;
		3、字体加粗
			font-weight:normal/bold/vaule; 一般用bold不用vaule值。normal用于去加粗字体。
		4、字体样式
			font-style:normal/italic  ;正常/斜体
		*5、font按顺序简写：font:font-style font-variant font-weight font-size/行高 font-family。不方便用
	文本属性：
		1、文本颜色 color:red;
		2、文本排列 text-align:left/right/center; 文字、图片、input <span>等行内元素内容左右对齐,对div嵌套的子元素div中的字也起作用
								对块元素中不起作用（除ie6）。
		3、文字修饰 text-decoration:none/underline;还有上下中划线等
		4、行高：line-height:value;
			1、单行文字，value与div高度相同时，垂直居中
			2、多行文字，增加行高间距。
		5、首行文本缩进 text-indent:value;
			1、text-indent:2em;两个汉字
			2、多个段落需首行缩进，需在每一段加<p>标记
		6、文本阴影 text-shadow:h-shadow v-shadow blur(模糊) color; 立体感文字
		*文本溢出（一般用于英文，只针对横向溢出）：
			1、处理空白
				white-space:normal/nowrap; 换行否
			2、文本溢出
				text-overflow:clip/ellipsis;
				例：
				overflow:hidden; /*容器溢出隐藏,这是前提*/
				white-space:nowrap; /*nowrap不换行, normal 正常*/
				text-overflow:ellipsis;/*clip裁掉，ellipsis隐藏部分显示...*/
		*长单词换行：word-wrap:normal/break-word;
		*文本换行（英文）：word-break:normal/break-all/keep-all;

		css控制两个字间的距离，word-spacing://英文
					letter-spacing:2em;//中文
表格样式属性；
		1、通用样式：
			width
			height
			border
			paddding
			background
			vertical-align :top/middle/bottom  td和行内块元素使用：如图片文字middle对齐
			text-align
		2、表格特有样式：
			border-collapse:collapse合并、 sparate分开（合并双线）
			border-spacing:10px 20px;（相邻单元格间距，前提border-collapse:sparate）
			caption-side（表格标题位置）
				table特点：宽高可以自适应，设置了宽高，页会被内容撑开
			table-layout:fixed(宽高固定，长出内容会被隐藏，
			text-overflow:ellipsis 用...代替长出部分，
			在html里加title可看浮出的全部内容
			（表格布局固定：固定较高效，不固定较灵活）

				table{
					width:500px;height:400px;border:1px solid black;paddding:10px;color:#666;font-size:16px;
					font-family:"微软雅黑";
					border-collapse:collapse;/*让table的双线边框合并成单线,sparate不合并*/
					border-spacing:10px 20px;/*相邻单元格间距，前提border-collapse:sparate**/
					caption-side:top;/*bottom*/
					table-layout:fixed;/*定义宽高后，固定表格布局，效率相对高；auto自动表格布局*/
				}
				td{
					border:1px solid blue;/*加给tr是不好使的，只能加给td*/
					vertical-align:top;/*垂直对齐，文字上下middle，bottom*/
					text-align:center; /*文字左右*/
					width:33%;
					overflow:hidden;
					text-overflow:ellipsis;/*大于单元格长度时用...替代，可添加title来浮动显示全部值*/
				}
表单样式属性：<ul> <li>自带margin padding
		1、list-style:none; 去掉列表项目符号
		*2、list-style-type:circle/disk/square;定义列表项标志。
		*3、list-style-image:url()序列图片，一般不用，用背景图
		*4、list-style-position:inside/outside;列表项在里面还是外面

定位：
	定义：定义元素框相对于其正常位置应该出现的位置，或者相对于父元素、另一个元素、浏览器本身的位置。
		-普通流定位
		-浮动定位
		-相对定位
		-绝对定位
	属性：position:static（默认）/relative/absolute/fixed;规定元素的定位类型
		偏移属性:top/bottom/left/right;定义元素偏移位置
		z-index:value;设置元素堆叠顺序
		float/clear:浮动定位属性。
	1、普通流定位：
		页面中的块级元素框从上到下一个一个的排列。
		-每一个块级元素都会出现在一个新行中。比如p div
		-元素框之间的垂直距离是有框的垂直外边距计算出来的。
		内联元素将在一行从左到右水平布局。
		-不需要从新行开始
		-可以使用水平内边距、边框和外边距调整他们的距离，没有width和height属性。
	2、浮动定位：在CSS中，任何元素都可以浮动。
		浮动定位是指：
			-将元素排列在普通流之外，即元素将脱离普通文本流。
			-元素将不在页面占空间
			-如果包含框太窄，浮动框会自动下移。
			-浮动框可以向左右移动，直到他碰到包含框或其他元素的边框为止。
			-可将浮动元素放在包含框的左边或右边。
			-浮动元素不会重叠，不会上下浮动，外边缘不会超出父元素。
		float元素：浮动通过float元素实现。
			-float:none/left/right
		clear属性：消除浮动bug。
			-用于清除浮动带来的影响，定义元素的哪边不允许出现浮动。
			-clear:none/left/right/both
		float与overflow:
			-包含框内的元素被应用了float之后，就不占位置，包含框的高度如果没有定义，会发生变化，变为0；
			-可设置包含框的overflow属性。overflow:hidden;这样可实现包含框高度随内部float高度变化。
	浮动的其他说明：行内元素加浮动后，会变为块元素，本来不可以设置宽高，现在就可以设置了。
	3、相对定位 :position:relative;
		-元素原本所占空间扔保留，不会影响周围元素
		-元素使用偏移属性定义位置
			-相对于原来的位置进行上下左右偏移。（相对于自己）
		例：<div style="position:relative;left:10px;top:20px;"></div>
			#d1{
				border-color:red;
				position:relative;/*实现相对定位*/
			}
			#d1:hover{/*鼠标滑过时改变位置*/
				top:5px;
				left:5px;
			}
	4、绝对定位:position:absolute;
		-元素从普通流中移除，不占用空间，会影响周围元素。可重叠
		-使用偏移属性固定位置
			-相对于最近的已定位父元素定位（相对于外层包含的父元素）
				【父元素必须已定位，如未定位，会再找上一层已定位父元素，父元素定位一般用relative,此属性不影响其他元素】。
			-如果没有已定位父元素，可相对于body。
				例	#parent{
						width:400px;
						height:400px;
						position:relative;
						/*要实现子元素相对于父元素定位，父元素要先定位。用relative，不加top等，不影响原来元素*/
					}
					#d2{
						position:absolute;
						bottom:10px;
						right:2px;
					}
	5、固定定位：position:fixed;
		-元素相对屏幕定位。
		-元素从普通流中移除，不占用空间
		-当用户向下滚定页面是，元素框不随着移动。
	6、堆叠顺序：z-index:value;
		-	一旦修改了元素的定位方式，元素可能发生堆叠。
		-	z-index要生效，必须是已定位元素。
		-	使用z-index来控制元素重叠顺序。
			-值为数值，数值越大，堆叠顺序越高，离用户越近，可设置负值。
	元素需要有定位，但不希望元素有任何变化，可用relative
显示：
	显示方式："一切皆为框"页面上所有的元素都可以显示为框。
	块级元素：
		-<div>、<h1>、<p>
		-这些元素显示为一块内容，即“块框”
	内联元素/行内元素：
		-<span>、<a>等
		-内容显示在行内
	行内块元素：-<img/>
		可以使用display来改变元素框的显示方式。
	<display>属性：display:none/block/inline/inline-block;
		-none:让生成的元素没有框，该内容不再显示切不占用文档的空间。
		-block:让行内元素(比如<a>)显示的像块级元素一样。
		-inline:让块级元素(比如<p>)显示的像内联元素一样。
		-inline-block:行内块元素。
		-table :作为块级表格来显示，可使父元素被浮动或绝对定位不占位置的的元素撑开
		说明：行内块并不能代替浮动：1、不能像块级元素一样能单独调整上下margin---【验证可以调整】。---但可用vertical-align实现效果
						2、低版本有兼容问题，低版本只支持行内元素变为行内快
		用法：一般都是把行内元素变为行内块来用
	<visibility>属性：visibility：visible/hidden/collapse
		-visible:元素可见,默认属性
		-hidden：元素不可见，但占用位置。可与display:none对比
		-collapse：用于表格，删除一行，不影响布局
	<opacity>属性：设置元素 不透明级别
		-opacity:value;  value规定不透明度。从 0.0 （完全透明）到 1.0（完全不透明）。，子元素会继承父元素的透明度。
		技巧：如果想子元素与父元素透明度不一样，可以设置两个重叠元素来实现，而不是嵌套使子元素继承父元素的透明度。
	<vertical-align>元素：
		1、设置单元格内容
		2、对【行内块级元素】，如<img>可设置垂直对齐方式。这里的对齐指的是同一行的元素的相对位置，如top是指都在top。
		如果行内元素加了vertical-align，可分别定义margin-top/bottom,不会影响同一行的其他元素。
		例：
		img{/*一般这两个结合使用，定义行内块元素的不同垂直高度，或对齐行内块元素*/
			vertical-align:top;
			margin-top:10px;
		}
光标：
	cursor元素：网页中不同的元素鼠标指上去是不一样的。默认div的话就是箭头，文本是竖线，链接和button是手
		-default -pointer:手 -crosshair：十字 -text：输入竖线 -wait:等待 -help：问号
		-n-resize:上下箭头，根据方向来的（n上，e右，s下，w左）
		-w-resize:左右箭头
		-ne-resize:右上箭头

display：显示方式
block/inline/inline-block/none/table
overflow:溢出元素框时的处理
visible/hidden/scroll/auto


------------------------------------ CSS3 高级--------------------------------------------
复杂选择器、内容生成、变形(transform)、过渡(transition)、动画(animation)
一、复杂选择器
	兄弟选择器：-选择 平级 元素的唯一办法
		-适用：通过已知元素选择之后的平级兄弟元素时
		1、相邻兄弟选择器:
			语法：p+b{/*平级的之后相邻的一个元素*/}
		2、通用兄弟选择器：
			语法：p~b{/*相同父元素下，p后所有的b生效*/}
	属性选择器：
		-通过元素的属性来定位元素
		1、通用：基本用不着，太泛了
			[title]{/*带有title属性的所有元素*/}
			p[id]{/*附带id属性的p元素。*/}
			p[id][class]{/*附带id和class属性的p元素*/}

		2、自定义：4种
			1、精确条件：
				p[title="summary"]:附带title属性的p元素，且title的值为summary。

			2、模糊条件：
				开头：p[class^="b"]：	class属性以b开头的所有p元素。
				包含：p[class*="b"]： class属性包含b串的所有p元素
				结尾：p[class$="b"]： class属性以b结尾的所有p元素。
		3、模糊条件：很少用：只有class能用着，但没必要，直接用点就可以了
			p[class~=" 单词 "]；附带class属性的p元素，class属性是空格隔开的单词列。
			p[class!="value"]
	伪类选择器：
		伪类：是一种特殊状态，触发时状态生效，触发消失时，状态消失
		1、目标伪类：
			-:target 选择当前正在被跳转到的锚点元素
				1、一次只能选择一个元素，如果有同名的name锚点，只选择第一个
				2、唯一一个可以触发其他元素样式变化的伪类
				3、target是要定义在锚点上，锚点被改变
				4、锚点个性化定义可以用 E:target
				5、触发的是a元素，锚点可以用id和name定义
			示例：	css
				:target{
					width:200px;
					height:200px;
					border:1px solid red;
				}
				a:target{/*a元素在公共样式基础上再加block*/
					display:block;
					border:1px solid green;
				}
				<p><a href="#new1" >跳转到内容1 </a></p><br/>
				<p><a href="#new2" >跳转到内容2 </a></p><br/><br/>
				<a name="new1" >new1 </a>
				<div id="new2">new2</div>
				html
				<p><a href="#news1">跳转到内容1</a></p>
				<p><a href="#news2">跳转到内容2</a></p>
				<a name="news1">内容1.。。</a>
				<div id="news2">内容2.。。</div>
		2、结构伪类：匹配父元素的首个、最后一个、空、唯一子元素。
			- 必须要定义在子元素上
				p:first-child{} p元素是第一个子元素
				p:last-child{}
				p:only-child{}
				#补充：p:nth-child(3){}选择第几个子元素
					但IE8不能这样写，要写成：p:first-child+p+p{}后几个就加几个p
			-必须定义在父元素上
				p:empty{}  不包含任何内容的父元素，包括文字，空格
				如：<li>动漫</li> <li> </li> 不算空
		*3、元素状态伪类：
			例：
				input:enabled{};匹配每个input的已启用元素（大多用于表单）。
				input:disabled{};匹配每个input的被禁用元素(大多用于表单)。
				input:checked{};匹配没有已被选中的的元素（只用于单选按钮和复选框）。ie11支持，其他浏览器部分支持
		4、伪元素选择器：应用于文字，使网页看起来想杂志
			p:first-letter{};应用于p的第一个字母，常用于排版细节，如首字母突出等。
			P:first-line{};
			::selection{};被用户选中的部分呈现效果。只有背景颜色和字体颜色可以改，其他一般浏览器不支持改。会影响页面布局
		5、否定伪类：选择器:not([本选择器的条件])   /*有兼容问题，少用*/
				input:not([type="text"]){}
				<input type="text"/>
				满足其他选择器，不满足"条件"内容
			注意：not后不能有空格,()内容为[条件]，不能有其他。
内容生成
	-在元素内容的特殊位置（行前行尾）生成指定文字/图片内容
	1、伪元素：
		:before{content:内容}--在原内容开头生成
		:after{content:内容}--在原内容结尾生成
		内容可替换为：content: url() / "字符串" /计数器
		p:before{content:"台词；";}在p元素文字前，加“台词：”字样。

		计数器conter:只能计数阿拉伯数字，原理是通过数字累加实现递增
			step:1在父元素上定义计数器，并设置初始值 ：父元素{counter-reset:计算器名 初始值可省}
			step:2在要生成序号的选择器上定义序号的步进：选择器{counter-increment:计数器名 步进}
			step:3奖计数器用于内容生成中：
				选择器：before/after{content:"第"counter(计数器名)"章"}
			body{counter-reset:chapter 0;}
			h1{counter-increment:chapter 1;}/*设置计数器增量*/
			h1:before{content:"第"counter(chapter)"章";}
		例：body{
			counter-reset:num 7;
			}
			#tip~p{
				counter-increment:num 1;
			}
			#tip~p:before{content:"第"counter(num)"段"}

			<p id="tip">我是第p,不要计数</p>
			<p >我是第一张</p>
			<p >我是第二张</p>
			<p >我是第三张</p>
*多列 没用
	1、分割列
		column-count:3;大量文本被分割成三列显示。
		column-gap:45px;列间隔。
		column-rule:2px solid red;列规则。
		浏览器兼容性：internet10和opera支持多列属性，其它浏览器许加-moz-等标识。

三、变形(transform)----迅速变化
	变形：使元素改变位置、尺寸和形状的一种效果
	变形属性包括：
		1、旋转-rotate  2、位移-translate  3、缩放-scale  4、倾斜-skew
	参照原点：
		-默认情况，原点是中心点，或x,y,z轴的50%处。
		-3种方法：
			transform-origin:x坐标 y坐标   （一个，两个，三个分别表示所有轴，xy轴，xyz轴）
					 right bottom
					 100%  100%

		-例：
			transform-origin:20px 40px;
					 50% 50%;
					 top center;
	坐标原点：
		-2D默认坐标原点在元素左上角，3D模拟为教室左上角，三面墙延伸方向为正方向。
	坐标方向：
		-x轴右正左负，y轴下正上负。3D z轴靠近观察者为正
	角度：所有的角度，从坐标正方向上看，正值是顺时针，负值是逆时针。
	会导致坐标系同时改变的变形：
		-这些变形要放在其他变形之后
		1、rotate(ndeg)	同时旋转绘图坐标系方向
		如：
			 transform:translate(50px,0px) rotate(30deg);--translate不受影响,水平移动
			 transform:rotate(30deg) translate(50px,0px);--translate受影响，延旋转后的x轴向右下移动50px
		2、scale(倍数)  是改变绘图坐标的比例
	1、旋转 rotate
		旋转：围绕一个参照点，旋转指定角度
		语法：transform:rotate(ndeg)
			例：transform:rotate(45deg);
		!强调：旋转 同时旋转绘图坐标系方向，影响之后延坐标轴的其他变形

	  3D旋转：以指定坐标轴为圆心，旋转指定角度
		 语法：transform:rotateX(ndeg)
				  rotateY(ndeg)
				  rotateZ(ndeg) ==rotate(ndeg)

	2、位移 translate
		位移：延坐标轴方向，移动指定距离
		语法：
			transform:translate(x轴移动距离，y轴移动距离)
				  translateX(距离)
				  translateY(距离)
			例：transform:translate(50px,0px);
		!强调：位移不会影响其他元素布局位置，位移后，元素自动上一一个层次,可能会盖住周围元素。

	   3D位移：特指延z轴移动
		语法：transform:translate3d(x,y,z)	——三个方向的位移
			        translateZ(z)		——仅延z位移

	3、缩放 scale
		缩放：将指定坐标轴上的坐标缩放指定倍数
		语法：
			transform:scale(倍数)			 //等比例缩放
				  scale(x轴缩放倍数，y轴缩放倍数)//按x,y轴缩放
				  scaleX(倍数)
				  scaleY(倍数);
			倍数：0-1之间，缩小；>1，放大。
		 !强调：1、缩放受参照原点影响.2、缩放对后续变形有影响，改变了坐标比例
	   3D缩放：特指延z轴缩放
		语法：
			transform:scaleZ(倍数)——将z坐标x指定倍数

	4、倾斜 skew
		倾斜：延坐标轴方向，倾斜指定角度
		语法：
			transform:skew(ndeg)		//仅延x轴倾斜
				  skew(ndeg,ndeg)	//延x,y轴倾斜
				  skewX(ndeg)
				  skewY(ndeg)
		!强调：倾斜受参照原点影响。
			x轴方向：正角度向左倒，负角度向右倒
			y轴方向：正角度左侧向上斜，负角度左侧向下斜
	5、3D变形
		perspective属性：设置假定的人眼位置到投影平面的距离
		见素材：perspective.png:
			 虚线圆圈：实际物体
			 d: 表示假定人眼的位置到投影平面(网页)的距离
			 矩形方块：模拟投影平面(网页)
		如何使用:设置在需变形元素的父元素上。
			例：perspective:400px;/*父元素距离*/
			（Chrome Safari支持替代属性）

四、过渡(transition)	--缓慢变化 --简单动画
	过渡：从开始样式，经过指定时间后，缓慢过渡到结束样式
	语法：
		transition:要变化的属性名 持续时间 速度变化类型 [延迟时间]
		例：transition:transform 1s linear 2s;
	!强调：写在开始样式中，即产生变化的变化前元素中

	实现多个属性同时过渡：
		一、分别定义，每个属性要一一对应
			1、设置属性名：transition-property:属性名1,属性名2 ...;
			2、设置持续时间： transition-duration:n1s,n2s ...;
			3、设置速度变化类型： transition-timing-function:
				  备选值：ease: 先加速后减速
					  linear: 匀速变化
					  ease-in: 加速
					  ease-out: 减速
					  ease-in-out: 先加速后减速
			4、设置延迟开始时间：transition-delay:n1s,n2s ...;
		 二、整合transition属性：
		      transition: 属性名1 持续时间 速度类型 [延迟],
				  属性名2 持续时间 速度类型 [延迟],
				  ...
			例：transition:top 1s linear ,background-color 2s linear 1s;
		三、transition:all 0.5s;对本元素触发变化后，变化了的所有属性生效

五、动画(animation) --关键帧动画 --无规律变化
	关键帧：动画执行过程中，物体在某一位置上的特殊状态
	关键帧动画：使用连续的关键帧，控制物体连续的状态变化
	何时使用关键帧动画：
	      连续，有规律的过渡——过渡transition
	      无规律的连续变化——动画animation
	定义：2步
		1. 定义关键帧：
		@keyframes 动画名{
			 from{开始样式} /*或0%{}*/
			 n%{当动画进度播放到n%时的样式}
			 ...
			 to{结束样式}/*或100%{}*/
			}
		2. 触发动画：
			animation:动画名 持续时间 速度类型 延时 forwards（维持最后一帧状态*） infinite（循环播放*）
				alternate（交替往返播放， 单数次正向播放，双数次逆向回来*）
		触发如何使用：
			写在非伪类的选择器，页面加载后自动触发
			写在伪类中，伪类触发时才播放
	如何暂停动画：animation-play-state:paused 暂停/running 播放
		使用：加在正在播放动画的元素的伪类上
	设置重复播放次数：
		-webkit-animation:change 1s linear 2;/*2不加s，表示重复播放2次*/

	浏览器兼容性： @keyframes animation animation-play-state （Firefox IE兼容）
			@-webkit-keyframes -webkit-animation （Chrome Safari支持替代属性）
			@-o-keyframes -o-animation （Opera支持替代属性）



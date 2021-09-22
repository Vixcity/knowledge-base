## CSS 实现新拟态(Neumorphism) UI 风格
新拟态是一种图形样式，其原理是为界面的UI元素赋予真实感。

原生名词有几个叫法，Neumorphism / soft ui，翻译过来是新拟态或者是软ui。

国内的翻译叫，新拟物风格。

Neumorphism，是New +Skeuomorphism的组合词。

按照我个人的通俗理解，就是将界面的一部分凸起来，另一部分凹下去，通过光影形成的一种错落有致的拟物风格。

让人感到真实一种UI风格。

代表作是乌克兰设计师 Alexander Plyuto 在各平台发布的新作品「Skeuomorph Mobile Banking」

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109221409248.webp)  

新拟态 UI 风格与扁平、投影风格的对比

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109221409892.webp)  

从上面这张对比图可以看出，扁平风格就像是一张纸贴在墙面上，投影风格像是浮在半空中，而新拟态风格则像是墙面上直接凸起了一块。

要实现这种风格，精髓在于一个白色的阴影+一个常规阴影。一个示例如下图所示：

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109221410686.webp)  

### **新拟态 UI 风格的前端实现**

闲来无事，参照着以上的实现方式临摹了一下练手(代码如下)。后来发现居然已经有了在线工具直接生成这种风格的 css 代码[aru_59]，很强！

> Neumorphism css 在线生成器地址：https://neumorphism.io

我自己实现的效果：  

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/eXCSRjyNYcaL9HkgfbiaIDqWkYLxr79E62TrnX5RFwNWFWibf7cHSB06QEREicRrOs0iccQ2atugb9hOLZfZWHOjcA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

代码：

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		 <meta charset="UTF-8">
		 <meta name="viewport" content="width=device-width, initial-scale=1.0">
		 <title>新拟物风格</title>
		 <link rel="stylesheet" href="./font_ue8tdlm62pf/iconfont.css">
		 <style>
			 .container {
				 width: 1018px;
				 margin: 0 auto;
				 height: 660px;
				 background-color: #e6eef4;
			 }

			 .top {
				 height: 200px;
				 flex-direction: row;
			 }

			 .top-left {
				 width: 400px;
				 /* height: 400px; */
				 float: left;
				 /* background-color: rgb(92, 89, 89); */
				 margin: 50px;
				 border-radius: 39px;
				 background: #e6eef4;
				 box-shadow: 33px 33px 67px #cad1d7,
				 -33px -33px 67px #ffffff;
			 }

			 .d1,
			 .d2,
			 .d3,
			 .d4 {
				 display: inline-block;
				 width: 144px;
				 height: 144px;
				 border-radius: 50%;
				 background-color: red;
				 margin: 24px;
				 border-radius: 50%;
				 background: #e6eef4;
				 box-shadow: inset 14px 14px 22px #cfd6dc,
				 inset -14px -14px 22px #fdffff;
			 }

			 .aircraft {
				 font-size: 80px;
				 display: flex;
				 justify-content: center;
				 align-items: center;
				 /* margin-top: 10%; */
				 padding-top: 30px;
				 color: #4d8af0;
			 }

			 .top-right {
				 width: 400px;
				 height: 400px;
				 float: right;
				 background-color: green;
				 margin: 50px;
				 border-radius: 39px;
				 background: #e6eef4;
				 box-shadow: 33px 33px 67px #cad1d7,
				 -33px -33px 67px #ffffff;
			 }

			 .d21,
			 .d31,
			 .d41 {
				 display: inline-block;
				 width: 88px;
				 height: 88px;
				 border-radius: 50%;
				 background-color: red;
				 margin: 21px;
				 border-radius: 50%;
				 background: #e6eef4;
				 box-shadow: 6px 6px 12px #cad1d7,
				 -6px -6px 12px #ffffff;
			 }

			 .r-aircraft {
				 font-size: 40px;
				 display: flex;
				 justify-content: center;
				 align-items: center;
				 /* margin-top: 10%; */
				 padding-top: 24px;
				 color: #4d8af0;
			 }

			 p {
				 text-align: center;
				 font-size: 24px;
				 color: #576f87;
				 font-weight: bold;
			 }
		</style>
	</head>

	<body>
		 <div class="container">
		 	<div class="top">
		 		<div class="top-left">
		 			<span class="d1">
		 				<i class="iconfont aircraft"></i>
		 			</span>
					
		 			<span class="d2">
						<i class="iconfont aircraft"></i>
					</span>

				 	<span class="d3">
						<i class="iconfont aircraft"></i>
					</span>

					<span class="d4">
						<i class="iconfont aircraft"></i>
					</span>
		 		</div>
				
		 		<div class="top-right">
					<i class="iconfont aircraft"></i>
					<p>音乐</p>
					<span class="d21">
						<i class="iconfont r-aircraft"></i>
					</span>
					<span class="d31">
						<i class="iconfont r-aircraft"></i>
					</span>
					<span class="d41">
						<i class="iconfont r-aircraft"></i>
					</span>
				</div>
		 	</div>
		 </div>
	</body>
</html>
```

### 新拟态UI风格Css在线生成

地址：https://neumorphism.io/#e0e0e0

效果如下：

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109221419616.webp)  
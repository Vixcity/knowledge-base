## 如何设置DIV可编辑

如何让一个div变成可编辑状态，比如富文本的输入框就可以用可编辑的div（自定义一个富文本时可用），类似textare。  

有2种方案可以实现：1是通过contenteditable属性设置为true，2是利用css的user-modify属性。

### **方案一：contenteditable属性**

```html
<div  id="add-content" contenteditable="true">这里可以编辑</div>
```

contenteditable 属性是 html5 中的新属性，contenteditable 属性规定元素内容是否可编辑。

注释：如果元素未设置 contenteditable 属性，那么元素会从其父元素继承该属性。

js操作：

监听事件keydown 、textInput 、input

```html
<script>
 	var content = document.getElementById('add-content')
 	//注册中文的输入事件，
 	var isCN = false;
 	
	content.addEventListener('compositionstart',function(){
		isCN = true;
	 });
	 
	 content.addEventListener('compositionend',function(){
	 	isCN = false;
	 })

 	//注册文本输入事件，获取光标的起止偏移数据,如果是非中文以及超出长度的输入，则撤销本次操作
	 var txtAnchorOffset, txtFocusOffset;
	 content.addEventListener("textInput",function(event){
		 var _sel = document.getSelection();
		 txtAnchorOffset = _sel.anchorOffset;
		 txtFocusOffset = _sel.focusOffset;
		 //必须加上isCN的判断，否则获取不到正确的光标数据
		 if(!isCN && this.textContent.length >= noteMax){
			 event.preventDefault();
		 }
	 });

	//注册粘贴事件，获取粘贴数据的长度
 	var pastedLength; 

 	content.addEventListener("paste",function(event){
		 if(!event.clipboardData) return;
		 pastedLength = event.clipboardData.getData('Text').length;
	 });

	 //注册输入事件，对输入的数据进行
	 content.addEventListener("input",function(event){
	 setTimeout(function(){
		 if(!isCN){

			 var _this = content;
			 if(_this.textContent.length > noteMax){

				 var data = _this.textContent;
				 if(pastedLength > 1){

					 oldDate = data.slice(0, txtAnchorOffset) + data.slice(txtFocusOffset+pastedLength, data.length);
					 //粘贴字符串长度置为0，以免影响到下一次判断。
					 pastedLength = 0;
				 } else {
					oldDate = data.slice(0, txtAnchorOffset) + data.slice(txtFocusOffset, data.length);
				 }

				 //再次截取最大长度字符串，防止溢出
				 _this.textContent = oldDate.slice(0, noteMax);
				 //光标移动到起始偏移位置
				 document.getSelection().collapse(_this.firstChild, txtAnchorOffset);
				 47
		 }
 	}
 }, 0);
 //content.focus(); 
 });
 </script>
```

### **方案二：css中user-modify属性**

```html
<div id="add-content" >这里可以编辑</div>
```

user-modify属性，用来控制用户能否对页面文本进行编辑。与标签的contentEditable属性类似。语法如下：  

```css
user-modify: read-only | read-write | read-write-plaintext-only
```

参数说明：

| 属性 | 代表内容 |
| :-----| :-----|
| read-only | 内容只读 | 
| read-write  | 内容可读写。(支持富文本) | 
| read-write-plaintext-only | 内容可读写，但粘贴内容中的富文本格式（如文本的颜色、大小，图片等）会丢失。内容类似于以纯文本显示。|
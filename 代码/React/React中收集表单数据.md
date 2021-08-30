页面中所有输入类的 DOM ，都是非受控类组件，现用现取，例如 input， textarea……

```javascript
 // 1.创建组件
 // 页面中所有输入类的DOM，都是非受控类组件，现用现取
 class Login extends React.Component{
	 render(){
		 return (
			 <form action='#test' onSubmit={ this.handleSubmit }>
				 用户名：<input ref={c => this.username=c} type="text" name="username"/>
				 <br/><br/>
				 密码：<input ref={c => this.password=c} type="password" name="password"/>
				 <br/><br/>
				 <button>登录</button>
			 </form>
		 )
	 }
  
	 handleSubmit = (event) => {
		// 阻止表单提交
	 	event.preventDefault() 
		const { username,password } = this
		alert(`你输入的用户名是: ${username.value},你输入的密码是: ${password.value}`)
	 }
}

 // 2.渲染组件到页面
 ReactDOM.render(<Login />,document.getElementById('test'))
```

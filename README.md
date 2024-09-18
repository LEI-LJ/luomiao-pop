##后续添加说名
引入组件
<luomiaoPop ref="luomiaoPop" backgroundColor="#212531" @uploadImg="uploadImg"></luomiaoPop>
<button @click="handleOpen">点击</button>

调用事件
	handleOpen(){ 		
 this.$refs.luomiaoPop.open('bottom')
 },
  uploadImg() {
				console.log("===>uploadImg");
  }

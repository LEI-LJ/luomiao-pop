<template>
	<view class="content">
		<luomiaoPop ref="luomiaoPop" backgroundColor="#212531" @uploadImg="uploadImg"></luomiaoPop>
		<button @click="handleOpen">点击</button>
	</view>
</template>

<script>
	import luomiaoPop from '../../components/luomiao-pop/cmp/luomiao-pop.vue'
	export default {
		components: {
			luomiaoPop
		},
		data() {
			return {
			}
		},
		onLoad() {

		},
		methods: {
			handleOpen() {
				this.$refs.luomiaoPop.open('bottom')
			},
			uploadImg() {
				console.log("===>uploadImg");
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
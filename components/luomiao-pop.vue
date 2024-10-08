<template>
	<view v-if="showPopup" class="uni-popup" :class="[popupstyle, isDesktop ? 'fixforpc-z-index' : '']">
		<view @touchstart="touchstart">
			<uni-transition key="1" v-if="maskShow" name="mask" mode-class="fade" :styles="maskClass"
				:duration="duration" :show="showTrans" @click="onTap" />
			<uni-transition key="2" :mode-class="ani" name="content" :styles="transClass" :duration="duration"
				:show="showTrans" @click="onTap">
				<view class="uni-popup__wrapper" :style="{ backgroundColor: bg }" :class="[popupstyle]" @click="clear">
					<view class="tit">
						<text class="text">上传形象建议 </text>
						<uni-icons type="clear" size="30" color="#989898" @click="handleClone"></uni-icons>
					</view>
					<view class="example">
						<text class="example-tit">形象示例</text>
						<view style="
								margin-top: 20rpx;
								background-color: #4a4c58;
								display: flex;
								justify-content: space-between;
								overflow: hidden;
								border-radius: 20rpx;
							">
							<image style="height: 30vh; overflow: hidden; border-radius: 20rpx; width: 50%" src="">
							</image>
							<view class="tit-area">
								<view class="rig-text"><uni-icons color="#45c04e" type="checkbox-filled"
										size="24"></uni-icons> 正面站立</view>
								<view class="rig-text"><uni-icons color="#45c04e" type="checkbox-filled"
										size="24"></uni-icons> 全身照</view>
								<view class="rig-text"><uni-icons color="#45c04e" type="checkbox-filled"
										size="24"></uni-icons> 身体无遮挡</view>
								<view class="rig-text"><uni-icons color="#45c04e" type="checkbox-filled"
										size="24"></uni-icons> 无俯仰角</view>
							</view>
						</view>
					</view>
					<view class="example">
						<text class="example-tit">形象示例</text>
						<view style="display: flex; justify-content: space-between; gap: 20rpx; margin-top: 20rpx">
							<view class="item">
								<image style="height: 200rpx; overflow: hidden; border-radius: 24rpx; width: 150rpx"
									src=""></image>
								<view class="txt"><uni-icons color="#eb382e" type="clear" size="24"></uni-icons>
									侧脸侧身</view>
							</view>
							<view class="item">
								<image style="height: 200rpx; overflow: hidden; border-radius: 24rpx; width: 150rpx"
									src=""></image>
								<view class="txt"><uni-icons color="#eb382e" type="clear" size="24"></uni-icons>
									光线暗淡</view>
							</view>
							<view class="item">
								<image style="height: 200rpx; overflow: hidden; border-radius: 24rpx; width: 150rpx"
									src=""></image>
								<view class="txt"><uni-icons color="#eb382e" type="clear" size="24"></uni-icons>
									手拿物品</view>
							</view>
							<view class="item">
								<image style="height: 200rpx; overflow: hidden; border-radius: 24rpx; width: 150rpx"
									src=""></image>
								<view class="txt">
									<uni-icons color="#eb382e" type="clear" size="24"></uni-icons>
									复杂背景
								</view>
							</view>
						</view>
					</view>
					<view class="desc"> 本功能将可能采集敏感信息用于AI合成，如上传非本人信息，请确保已获得权利人的明确许可。 </view>
					<button class="uploadImg" @click="uploadImg">上传图片</button>
					<!-- <slot /> -->
				</view>
			</uni-transition>
		</view>
	</view>
</template>

<script>
	/**
	 * PopUp 弹出层
	 * @description 弹出层组件，为了解决遮罩弹层的问题
	 * @tutorial https://ext.dcloud.net.cn/plugin?id=329
	 * @property {String} type = [top|center|bottom|left|right|message|dialog|share] 弹出方式
	 * 	@value top 顶部弹出
	 * 	@value center 中间弹出
	 * 	@value bottom 底部弹出
	 * 	@value left		左侧弹出
	 * 	@value right  右侧弹出
	 * 	@value message 消息提示
	 * 	@value dialog 对话框
	 * 	@value share 底部分享示例
	 * @property {Boolean} animation = [true|false] 是否开启动画
	 * @property {Boolean} maskClick = [true|false] 蒙版点击是否关闭弹窗(废弃)
	 * @property {Boolean} isMaskClick = [true|false] 蒙版点击是否关闭弹窗
	 * @property {String}  backgroundColor 主窗口背景色
	 * @property {String}  maskBackgroundColor 蒙版颜色
	 * @property {Boolean} safeArea		   是否适配底部安全区
	 * @event {Function} change 打开关闭弹窗触发，e={show: false}
	 * @event {Function} maskClick 点击遮罩触发
	 */

	import uniTransition from './uni-transition/uni-transition.vue'
	export default {
		name: 'uniPopup',
		emits: ['change', 'maskClick', 'uploadImg'],
		props: {
			// 开启动画
			animation: {
				type: Boolean,
				default: true
			},
			// 弹出层类型，可选值，top: 顶部弹出层；bottom：底部弹出层；center：全屏弹出层
			// message: 消息提示 ; dialog : 对话框
			type: {
				type: String,
				default: 'center'
			},
			// maskClick
			isMaskClick: {
				type: Boolean,
				default: null
			},
			// TODO 2 个版本后废弃属性 ，使用 isMaskClick
			maskClick: {
				type: Boolean,
				default: null
			},
			backgroundColor: {
				type: String,
				default: 'none'
			},
			safeArea: {
				type: Boolean,
				default: true
			},
			maskBackgroundColor: {
				type: String,
				default: 'rgba(0, 0, 0, 0.4)'
			},
		},

		watch: {
			/**
			 * 监听type类型
			 */
			type: {
				handler: function(type) {
					if (!this.config[type]) return
					this[this.config[type]](true)
				},
				immediate: true
			},
			isDesktop: {
				handler: function(newVal) {
					if (!this.config[newVal]) return
					this[this.config[this.type]](true)
				},
				immediate: true
			},
			/**
			 * 监听遮罩是否可点击
			 * @param {Object} val
			 */
			maskClick: {
				handler: function(val) {
					this.mkclick = val
				},
				immediate: true
			},
			isMaskClick: {
				handler: function(val) {
					this.mkclick = val
				},
				immediate: true
			},
			// H5 下禁止底部滚动
			showPopup(show) {
				// #ifdef H5
				// fix by mehaotian 处理 h5 滚动穿透的问题
				document.getElementsByTagName('body')[0].style.overflow = show ? 'hidden' : 'visible'
				// #endif
			}
		},
		components: {
			uniTransition
		},
		data() {
			return {
				duration: 300,
				ani: [],
				showPopup: false,
				showTrans: false,
				popupWidth: 0,
				popupHeight: 0,
				config: {
					top: 'top',
					bottom: 'bottom',
					center: 'center',
					left: 'left',
					right: 'right',
					message: 'top',
					dialog: 'center',
					share: 'bottom'
				},
				maskClass: {
					position: 'fixed',
					bottom: 0,
					top: 0,
					left: 0,
					right: 0,
					backgroundColor: 'rgba(0, 0, 0, 0.4)'
				},
				transClass: {
					position: 'fixed',
					left: 0,
					right: 0,
					'border-radius': 25 + 'rpx',
					overflow: 'hidden',
				},
				maskShow: true,
				mkclick: true,
				popupstyle: this.isDesktop ? 'fixforpc-top' : 'top'
			}
		},
		computed: {
			isDesktop() {
				return this.popupWidth >= 500 && this.popupHeight >= 500
			},
			bg() {
				if (this.backgroundColor === '' || this.backgroundColor === 'none') {
					return 'transparent'
				}
				return this.backgroundColor
			}
		},
		mounted() {

			const fixSize = () => {
				const {
					windowWidth,
					windowHeight,
					windowTop,
					safeArea,
					screenHeight,
					safeAreaInsets
				} = uni.getSystemInfoSync()
				this.popupWidth = windowWidth
				this.popupHeight = windowHeight + (windowTop || 0)
				// TODO fix by mehaotian 是否适配底部安全区 ,目前微信ios 、和 app ios 计算有差异，需要框架修复
				if (safeArea && this.safeArea) {
					// #ifdef MP-WEIXIN
					this.safeAreaInsets = screenHeight - safeArea.bottom
					// #endif
					// #ifndef MP-WEIXIN
					this.safeAreaInsets = safeAreaInsets.bottom
					// #endif
				} else {
					this.safeAreaInsets = 0
				}
			}
			fixSize()
			// #ifdef H5
			// window.addEventListener('resize', fixSize)
			// this.$once('hook:beforeDestroy', () => {
			// 	window.removeEventListener('resize', fixSize)
			// })
			// #endif
		},
		// #ifndef VUE3
		// TODO vue2
		destroyed() {
			this.setH5Visible()
		},
		// #endif
		// #ifdef VUE3
		// TODO vue3
		unmounted() {
			this.setH5Visible()
		},
		// #endif
		created() {
			// this.mkclick =  this.isMaskClick || this.maskClick
			if (this.isMaskClick === null && this.maskClick === null) {
				this.mkclick = true
			} else {
				this.mkclick = this.isMaskClick !== null ? this.isMaskClick : this.maskClick
			}
			if (this.animation) {
				this.duration = 300
			} else {
				this.duration = 0
			}
			// TODO 处理 message 组件生命周期异常的问题
			this.messageChild = null
			// TODO 解决头条冒泡的问题
			this.clearPropagation = false
			this.maskClass.backgroundColor = this.maskBackgroundColor
		},
		methods: {
			handleClone() {
				this.close('bottom')
			},
			uploadImg() {
				this.$emit('uploadImg');
			},
			setH5Visible() {
				// #ifdef H5
				// fix by mehaotian 处理 h5 滚动穿透的问题
				document.getElementsByTagName('body')[0].style.overflow = 'visible'
				// #endif
			},
			/**
			 * 公用方法，不显示遮罩层
			 */
			closeMask() {
				this.maskShow = false
			},
			/**
			 * 公用方法，遮罩层禁止点击
			 */
			disableMask() {
				this.mkclick = false
			},
			// TODO nvue 取消冒泡
			clear(e) {
				// #ifndef APP-NVUE
				e.stopPropagation()
				// #endif
				this.clearPropagation = true
			},

			open(direction) {
				// fix by mehaotian 处理快速打开关闭的情况
				if (this.showPopup) {
					clearTimeout(this.timer)
					this.showPopup = false
				}
				let innerType = ['top', 'center', 'bottom', 'left', 'right', 'message', 'dialog', 'share']
				if (!(direction && innerType.indexOf(direction) !== -1)) {
					direction = this.type
				}
				if (!this.config[direction]) {
					console.error('缺少类型：', direction)
					return
				}
				this[this.config[direction]]()
				this.$emit('change', {
					show: true,
					type: direction
				})
			},
			close(type) {
				this.showTrans = false
				this.$emit('change', {
					show: false,
					type: this.type
				})
				clearTimeout(this.timer)
				// // 自定义关闭事件
				// this.customOpen && this.customClose()
				this.timer = setTimeout(() => {
					this.showPopup = false
				}, 300)
			},
			// TODO 处理冒泡事件，头条的冒泡事件有问题 ，先这样兼容
			touchstart() {
				this.clearPropagation = false
			},

			onTap() {
				if (this.clearPropagation) {
					// fix by mehaotian 兼容 nvue
					this.clearPropagation = false
					return
				}
				this.$emit('maskClick')
				if (!this.mkclick) return
				this.close()
			},
			/**
			 * 顶部弹出样式处理
			 */
			top(type) {
				this.popupstyle = this.isDesktop ? 'fixforpc-top' : 'top'
				this.ani = ['slide-top']
				this.transClass = {
					position: 'fixed',
					left: 0,
					right: 0,
					backgroundColor: this.bg,
					borderRadius: 25 + 'rpx',
					overflow: 'hidden',
				}
				// TODO 兼容 type 属性 ，后续会废弃
				if (type) return
				this.showPopup = true
				this.showTrans = true
				this.$nextTick(() => {
					if (this.messageChild && this.type === 'message') {
						this.messageChild.timerClose()
					}
				})
			},
			/**
			 * 底部弹出样式处理
			 */
			bottom(type) {
				this.popupstyle = 'bottom'
				this.ani = ['slide-bottom']
				this.transClass = {
					position: 'fixed',
					left: 0,
					right: 0,
					bottom: 0,
					'border-radius': 24 + 'rpx',
					overflow: 'hidden',
					paddingBottom: this.safeAreaInsets + 'px',
					backgroundColor: this.bg
				}
				// TODO 兼容 type 属性 ，后续会废弃
				if (type) return
				this.showPopup = true
				this.showTrans = true
			},
			/**
			 * 中间弹出样式处理
			 */
			center(type) {
				this.popupstyle = 'center'
				this.ani = ['zoom-out', 'fade']
				this.transClass = {
					position: 'fixed',
					/* #ifndef APP-NVUE */
					display: 'flex',
					flexDirection: 'column',
					/* #endif */
					bottom: 0,
					left: 0,
					right: 0,
					top: 0,
					justifyContent: 'center',
					alignItems: 'center'
				}
				// TODO 兼容 type 属性 ，后续会废弃
				if (type) return
				this.showPopup = true
				this.showTrans = true
			},
			left(type) {
				this.popupstyle = 'left'
				this.ani = ['slide-left']
				this.transClass = {
					position: 'fixed',
					left: 0,
					bottom: 0,
					top: 0,
					backgroundColor: this.bg,
					/* #ifndef APP-NVUE */
					display: 'flex',
					flexDirection: 'column'
					/* #endif */
				}
				// TODO 兼容 type 属性 ，后续会废弃
				if (type) return
				this.showPopup = true
				this.showTrans = true
			},
			right(type) {
				this.popupstyle = 'right'
				this.ani = ['slide-right']
				this.transClass = {
					position: 'fixed',
					bottom: 0,
					right: 0,
					top: 0,
					backgroundColor: this.bg,
					/* #ifndef APP-NVUE */
					display: 'flex',
					flexDirection: 'column'
					/* #endif */
				}
				// TODO 兼容 type 属性 ，后续会废弃
				if (type) return
				this.showPopup = true
				this.showTrans = true
			}
		}
	}
</script>
<style lang="scss">
	.uni-popup {

		position: fixed;
		/* #ifndef APP-NVUE */
		z-index: 99;

		/* #endif */
		&.top,
		&.left,
		&.right {
			/* #ifdef H5 */
			top: var(--window-top);
			/* #endif */
			/* #ifndef H5 */
			top: 0;
			/* #endif */
		}

		.uni-popup__wrapper {

			/* #ifndef APP-NVUE */
			display: block;
			/* #endif */
			position: relative;

			/* iphonex 等安全区设置，底部安全区适配 */
			/* #ifndef APP-NVUE */
			// padding-bottom: constant(safe-area-inset-bottom);
			// padding-bottom: env(safe-area-inset-bottom);
			/* #endif */
			&.left,
			&.right {
				/* #ifdef H5 */
				padding-top: var(--window-top);
				/* #endif */
				/* #ifndef H5 */
				padding-top: 0;
				/* #endif */
				flex: 1;
			}
		}
	}

	.fixforpc-z-index {

		/* #ifndef APP-NVUE */
		z-index: 999;
		/* #endif */
	}

	.fixforpc-top {
		border-radius: 24rpx;
		top: 0;

	}


	.uni-popup__wrapper {
		color: #fff;
		box-sizing: border-box;
		border-radius: 30rpx 30rpx 0 0;
		overflow: hidden;
		width: 100%;
		background-color: #212531;
		position: fixed;
		bottom: -100%;
		padding: 46rpx;
		transition: 0.3s all;

		.example {
			margin-top: 40rpx;

			.example-tit {
				color: #9ea2ad;
				font-size: 28rpx;
			}

			.tit-area {
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: left;
				margin-left: 50rpx;
				flex: 1;
				gap: 30rpx 0;

				.rig-text {
					display: flex;
					align-items: center;
					font-size: 24rpx;
				}

			}

			.item {
				.txt {
					display: flex;
					align-items: center;
					color: #cbced2;
					font-size: 24rpx;
				}
			}
		}

		.desc {
			margin: 20rpx 0;
			font-size: 24rpx;
			color: #686d81;
		}

		.uploadImg {
			background-color: #634cfe;
			color: #fff;
			border-radius: 50rpx;
		}

		.tit {
			display: flex;
			justify-content: space-between;
			align-items: center;
			font-size: 36rpx;
			font-weight: 700;
		}

		.top-area {
			margin-top: 104rpx;

			.Title {
				font-weight: 600;
				font-size: 48rpx;
				color: #16151f;
				line-height: 56rpx;
			}

			.TextSize {
				margin-top: 6rpx;
				margin-bottom: 64rpx;
			}
		}
	}
</style>
<template>
	<view class="player-box">
		<view class="background">
			<image class="bgimg" src="../../static/logo.png"></image>
		</view>
		<transition name="normal" @enter="enter" @after-enter="afterEnter" @leave="leave" @after-leave="afterLeave">
			<view class="normal-player" v-show="fullScreen">
				<view class="middle">
					<view class="middle-left">
						<view class="image-wrapper" ref="imgWrapper">
							<image src="../../static/logo.png" :class="rotateState">
						</view>
						<view class="lyric-wrapper">{{playInfo.name}}</view>
						<view class="lyric-wrapper-count">{{playInfo.view_time}}人 已学习</view>
					</view>
					<view class=""></view>
				</view>
				<view class="bottom" ref="bottom">
					<view class="progress-wrapper">
						<view class="progress-bar-wrapper">
							<!-- <progress-bar :percentage="10" @percentChange="percentChange"></progress-bar> -->
							<progress :percent="progress" stroke-width="4" activeColor="#8B8B8A" border-radius="2" />
							<view class="progress-bar-tip">
								<text>{{timeProgress | formatSecond}}</text>
								<text>{{maxTime | formatSecond}}</text>
							</view>
						</view>
					</view>
					<view class="control-wrapper">
						<view class="pre-next-icon">
							<image src="../../static/pre-song-icon.png" class="img" @click="prevSong"></image>
						</view>
						<view class="play-icon">
							<image :src="isPlaying ? '../../static/player-play-icon.png' : '../../static/player-pause-icon.png'" class="img" @click="togglePlaying"></image>
						</view>
						<view class="pre-next-icon">
							<image src="../../static/next-song-icon.png" class="img" @click="nextSong"></image>
						</view>
					</view>
				</view>
			</view>
		</transition>
	</view>
</template>

<script>
	import { realFormatSecond } from '../../utils/time/index.js'
	export default {
		props: {
			// 音频附属信息（如：名字、已收听人数...）
			playInfo: {
				type: Object,
				default: () => {
					return {}
				}
			},
			// 是否正在播放
			isPlaying: {
				type: Boolean,
				default: false
			},
			// 播放进度
			progress: {
				type: Number,
				default: 0
			},
			// 播放进度(时间格式化)
			timeProgress: {
				type: Number,
				default: 0
			},
			// 音频时长
			maxTime: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				playing: true,
				fullScreen: true,
			};
		},
		filters: {
			// 将整数转化成时分秒
			formatSecond(second = 0) {
			  return realFormatSecond(second);
			}
		},
		created() {
			console.log('是否正在播放:', this.isPlaying)
		},
		computed: {
			rotateState() {
				return this.isPlaying ? 'rotate' : 'rotate rotate-pause'
			},
		},
		methods:{
			getPosAndScale() {
				const targetWidth = 40    //图像缩略图宽度
				const width = window.innerWidth * 0.8 //图像宽度
				const scale = targetWidth / width

				const paddingLeft = 40
				const x=-(window.innerWidth/2-paddingLeft)

				const paddingTop = 80
				const paddingBottom = 30
				const y=window.innerHeight - paddingTop - width / 2 - paddingBottom
				return { x,y,scale }
			},
			enter(el,done) {
				const {x, y, scale} = this.getPosAndScale()
				let animation = {
					0: {
						transform:`translate(${x}px,${y}px) scale(${scale})`
					},
					60: {
						transform:"translate(0,0) scale(1.1)"
					},
					100: {
						transform:"translate(0,0) scale(1)",
					}
				}

				animations.registerAnimation({
					name: 'move',
					animation,
					presets: {
						duration: 400,
						easing: 'linear'
					}
				})

				animations.runAnimation(this.$refs.imgWrapper, 'move', done)
			},
			afterEnter() {
				animations.unregisterAnimation('move')
				this.$refs.imgWrapper.style.animation = ''
			},
			leave(el,done) {
				const {x, y, scale} = this.getPosAndScale()
				this.$refs.imgWrapper.style.transition = 'all 0.4s'
				this.$refs.imgWrapper.style.transform = `translate(${x}px,${y}px) scale(${scale})`
				this.$refs.imgWrapper.addEventListener('transitionend', done)
			},
			afterLeave() {
				this.$refs.imgWrapper.style.transition = ''
				this.$refs.imgWrapper.style.transform = ''
			},
			percentChange() {
				console.log('进度条改变')
			},
			prevSong() {
				console.log('上一曲')
				this.$emit('prevSong')
			},
			togglePlaying() {
				console.log('播放')		
				this.$emit('playSong')
			},
			nextSong() {
				console.log('下一曲')
				this.$emit('nextSong')
			},
		}
	}
</script>

<style>
	.player-box {
		width: 100%;
		height: 900rpx;
	}
	.normal-player{
		position: fixed;
		z-index: 100;
		left: 0;
		top: 0;
		width: 100%;
		height: 900rpx;
		padding-top: 44px;
	}

	.background{
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 900rpx;
		/* opacity: 0.7; */
		filter: blur(20px);
		-webkit-mask-image: -webkit-gradient(linear,left top,left bottom,from(rgba(0,0,0,1)),to(rgba(0,0,0,0)));
	}
	.img{
		width: 100%;
		height: 100%;
	}
	.bgimg {
		width: 100%;
		height: 100%;
		object-fit: cover; 
		filter: blur(20px);
		transform: scale(1.1);
	}

	.top{
		position: relative;
		margin-bottom: 25px;
		color: #fff;
		text-align: center;
	}
	.back{
		position: absolute;
		height: 40px;
		width: 60px;
		line-height: 40px;
	}
	.back i{
		font-size: 22px;
	}
	.top h2{
		width: 70%;
		height: 40px;
		line-height: 40px;
		margin: 0 auto;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
		font-size: 18px;
		font-weight: normal;
	}
	.top h3{
		width: 70%;
		height: 20px;
		line-height: 20px;
		margin: 0 auto;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
		font-size: 15px;
		font-weight: normal;
	}

	.middle{
		width: 100%;
		text-align: center;
		padding-bottom: 68rpx;
	}
	.middle-left {
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.image-wrapper{
		width: 50vw;
		height: 50vw;
		margin: 12% auto 6%;
		background: rgba(255,255,255,0.3);
		display: flex;
		justify-content: center;
		border-radius: 50%;
		align-items: center;
	}
	.image-wrapper img{
		width: calc(100% - 32rpx);
		height: calc(100% - 32rpx);
		border-radius: 50%;
		border: 10px solid hsla(0,0%,100%,.1);
	}
	.rotate{
		animation: rotateAnimation 22s linear infinite;
		border-radius: 50%;
		width: calc(100% - 32rpx);
		height: calc(100% - 32rpx);
	}
	.rotate-pause{
		animation-play-state: paused
	}
	.pre-next-icon {
		width: 48rpx;
		height: 48rpx;
	}
	.play-icon {
		width: 96rpx;
		height: 96rpx;
	}
	@keyframes rotateAnimation{
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
	.middle .lyric-wrapper{
		color: #0B0421;
		font-weight: 600;
		font-family: PingFangSC-Medium,PingFang SC;
		letter-spacing: 2px;
		font-size: 32rpx;
	}
	.lyric-wrapper-count {
		font-size: 24rpx;
		color: #8B8B8A;
		margin-top: 24rpx;
	}

	.bottom{
		text-align: center;
	}
	.progress-wrapper{
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 80%;
		margin: 0 auto;
	}
	.progress-wrapper .time{
		width: 30px;
		height: 30px;
		line-height: 30px;
		color: #fff;
		font-size: 12px;
	}
	.progress-bar-wrapper{
		flex-grow: 1;
		height: 14rpx;
		padding: 0 5px;
	}
	.progress-bar-tip {
		flex: 1;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
		font-size: 24rpx;
		color: #8B8B8A;
		margin-top: 16rpx;
		height: 20rpx;
	}

	.bottom .control-wrapper{
		display: flex;
		padding: 64rpx 60rpx;
		justify-content: space-around;
		align-items: center;
	}
	.bottom .control-wrapper .icon{
		flex-grow: 1;
		height: 41px;
		line-height: 41px;
		text-align: center;
		color: #31c27c;
	}
	.control-wrapper .icon:first-child i{
		font-size: 28px;
	}
	.control-wrapper .icon:last-child i{
		font-size: 28px;
	}
	.control-wrapper .icon i{
		font-size: 33px;
	}
	.control-wrapper .icon.center i{
		font-size: 42px;
	}
	.control-wrapper .icon .red{
		color: #d93f30;
	}
	
	.normal-enter-active,.normal-leave-active{
		transition: all 0.4s;
	}
	.normal-enter-active .top,.normal-enter-active .bottom{
		transition: all 0.4s cubic-bezier(0.86, 0.18, 0.82, 1.32)
	}
	.normal-leave-active .top,.normal-leave-active .bottom{
		transition: all 0.4s cubic-bezier(0.86, 0.18, 0.82, 1.32)
	}
	.normal-enter,.normal-leave-to{
		opacity: 0
	}
	.normal-enter .bottom,.normal-leave-to .bottom{
		/*为什么这句话没有作用*/
		transform: translateY(100%)
	}
	.normal-enter .top,.normal-leave-to .top{
		transform: translateY(-100%)
	}
</style>

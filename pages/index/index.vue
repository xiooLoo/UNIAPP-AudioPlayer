<template>
	<view class="content">
		<Player
			:playInfo="playInfo" 
			:progress="currentAudio.progress" 
			:timeProgress="currentAudio.timeProgress"
			:isPlaying="currentAudio.isPlaying"
			:maxTime="currentAudio.maxTime"
			@prevSong="prevSong" 
			@playSong="playSong" 
			@nextSong="nextSong"
		/>
	</view>
</template>

<script>
	import Player from '@/components/Player/Player.vue'
	const innerAudioContext = uni.createInnerAudioContext();
	export default {
		components: {
			Player
		},
		data() {
			return {
				playInfo: {
					name: 'xiooLoo',
					view_time: 26,
				},
				currentAudio: {
					poster: 'https://img-cdn-qiniu.dcloud.net.cn/uniapp/audio/music.jpg',
					name: '致爱丽丝',
					author: '暂无',
					src: 'https://img-cdn-qiniu.dcloud.net.cn/uniapp/audio/music.mp3',
					isPlaying: false, // 播放暂停按钮
					progress: 0, // 播放进度
					duration: 0, // 当前已播时长
					timeProgress: 0, // 未格式化播放进度(秒数)
					timer: null, // 播放进度计时器
					maxTime: 0, // 当前音频时长
				}
			}
		},
		onLoad() {
			let self = this
			innerAudioContext.src = self.currentAudio.src;
			innerAudioContext.onCanplay((res) => {
			  self.currentAudio.maxTime = Math.ceil(innerAudioContext.duration)
			});
			innerAudioContext.onPlay((res) => {
			  console.log('已开始播放:', res);
			  self.currentAudio.timer = setInterval(() => {
				  self.currentAudio.duration ++
				  self.currentAudio.timeProgress = self.currentAudio.duration 
				  self.currentAudio.progress = Math.floor((self.currentAudio.duration / self.currentAudio.maxTime) * 100)
				  console.log(self.currentAudio.progress);
			  }, 1000)
			  self.currentAudio.isPlaying = true
			});
			innerAudioContext.onPause(() => {
			  console.log('已暂停播放');
			  clearInterval(self.currentAudio.timer)
			  self.currentAudio.isPlaying  = false
			});
			innerAudioContext.onEnded(() => {
			  console.log('音频播放结束');
			  self.currentAudio.isPlaying  = false
			  self.currentAudio.duration = 0
			  clearInterval(self.currentAudio.timer)
			});
			innerAudioContext.onError((res) => {
				self.currentAudio.isPlaying = false
				console.log('播放出现异常：', res)
			});
		},
		methods: {
			prevSong() {
				console.log('prevSong')
			},
			playSong() {
				if (this.currentAudio.isPlaying) {
					innerAudioContext.pause()
					return
				}
				innerAudioContext.play()
			},
			nextSong() {
				console.log('nextSong')
			},
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

<template>
	<view class="video_play">
		<!-- 背景图片 -->
		<image :src="videoObj.img" mode="widthFix"></image>

		<!-- 工具栏开始 -->
		<view class="video_tool">
			<view @click="handleMuted" :class="['iconfont',muted?'iconjingyin':'iconshengyin']"></view>
			<view class="iconfont iconzhuanfa">
				<button open-type="share"></button>
			</view>
		</view>
		<!-- 工具栏结束 -->

		<!-- 视频开始 -->
		<view class="video_wrap">
			<video :src="videoObj.video" objectFit="fill" controls :muted="muted"></video>
		</view>
		<!-- 视频结束 -->

		<!-- 下载开始 -->
		<view class="download">
			<view class="download_btn" @click="handleDownLoad">
				下载高清
			</view>
		</view>
		<!-- 下载结束 -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				videoObj: {},
				// 是否静音
				muted: false
			}
		},
		onLoad() {
			// console.log(getApp().globalData);
			this.videoObj = getApp().globalData.video;
		},
		methods: {
			// 开关声音
			handleMuted() {
				this.muted = !this.muted;
			},

			// 下载视频
			async handleDownLoad() {
				await uni.showToast({
					title: '下载中',
					icon: "none"
				});
				// 1.将远程文件下载到 小程序内存中
				const {
					tempFilePath
				} = (await uni.downloadFile({
					url: this.videoObj.video
				}))[1];
				// 2.将内存中的问价下载到本地上
				await uni.saveVideoToPhotosAlbum({
					filePath: tempFilePath,
				});

				await uni.showToast({
					title: '下载成功'
				})
			}
		}
	}
</script>

<style lang="scss">
	.video_play {
		position: relative;

		image {
			position: absolute;
			width: 100vh;
			height: 100vh;
			z-index: -1;
			// css3滤镜
			filter: blur(20rpx);
		}

		.video_tool {
			height: 80rpx;
			display: flex;
			justify-content: flex-end;
			margin: 20rpx 0;

			.iconfont {
				width: 80rpx;
				color: #fff;
				font-size: 50rpx;
				border-radius: 40rpx;
				background-color: rgba(0, 0, 0, .2);
				display: flex;
				justify-content: center;
				align-items: center;
				margin-right: 20rpx;
			}

			.iconzhuanfa {
				position: relative;

				button {
					position: absolute;
					width: 100%;
					height: 100%;
					opacity: 0;
				}
			}

		}

		.video_wrap {
			display: flex;
			justify-content: center;

			video {
				width: 360rpx;
				height: 600rpx;
			}
		}

		.download {
			display: flex;
			justify-content: center;
			align-items: center;
			margin-top: 30rpx;

			.download_btn {
				width: 360rpx;
				height: 80rpx;
				border-radius: 40rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				color: #fff;
				border: 2rpx solid #fff;
				background-color: rgba(0, 0, 0, .5);

			}
		}
	}
</style>

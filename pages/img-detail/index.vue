<template>
	<view>
		<!-- 用户信息开始 -->
		<view class="user_info">
			<view class="user_icon">
				<image src="https://pan.zealsay.com/zealsay/cover/7.jpg"></image>
			</view>
			<view class="user_desc">
				<view class="user_name">
					飞跃高山的鱼
				</view>
				<view class="user_time">
					{{imgDetail.cnTime}}
				</view>
			</view>
		</view>
		<!-- 用户信息结束 -->

		<!-- 高清大图 开始 -->
		<view class="high_img">
			<swiper-action @swiperAction="handleSwiperAction">
				<image :src="imgDetail.thumb" mode="aspectFill"></image>
			</swiper-action>
		</view>
		<!-- 高清大图 结束 -->

		<!-- 点赞 开始 -->
		<view class="user_rank">
			<view class="rank">
				<text class="iconfont icondianzan">{{imgDetail.rank}}</text>
			</view>
			<view class="user_collect">
				<text class="iconfont iconshoucang"></text>
			</view>
		</view>
		<!-- 点赞 结束 -->

     <!-- 下载开始 -->
     <view class="download">
     	<view class="download_btn" @click="handleDownLoad">
     		下载图片
     	</view>
     </view>
     <!-- 下载结束 -->
		 
		<!-- 专辑开始 -->
		<view class="album_wrap">
			<!-- 标题 -->
			<view class="album_title">
				相关
			</view>
			<!-- 内容 -->
			<view class="album_list">
				<view class="album_item">
					<view class="album_cover">
						<image :src="album.cover" mode="aspectFill"></image>
					</view>
					<view class="album_info">
						<view class="album_info_text">
							专辑
						</view>
						<view class="album_name">
							{{album.name}}
						</view>
						<text class="iconfont iconiconfontjiantou4"></text>
					</view>
				</view>

			</view>
		</view>
		<!-- 专辑结束 -->

		<!-- 热评开始 -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment_title">
				<text class="iconfont iconhot1"></text>
				<text class="comment_text">最热评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item" v-for="item in hot" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">
								{{item.user.name}}
							</view>
							<view class="user_time">
								{{item.cnTime}}
							</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" mode="">
							</image>
						</view>
					</view>
					<!-- 评论信息 -->
					<view class="comment_desc">
						<view class="comment_content">{{item.content}}</view>
						<view class="comment_like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 热评结束 -->

		<!-- 最新评论开始 -->
		<view class="comment new" v-if="comment.length">
			<view class="comment_title">
				<text class="iconfont iconpinglun"></text>
				<text class="comment_text">最新评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item" v-for="item in comment" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">
								{{item.user.name}}
							</view>
							<view class="user_time">
								{{item.cnTime}}
							</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" mode="">
							</image>
						</view>
					</view>
					<!-- 评论信息 -->
					<view class="comment_desc">
						<view class="comment_content">{{item.content}}</view>
						<view class="comment_like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最新评论结束 -->
		
		
	</view>
</template>

<script>
	import moment from "@/utils/moment.js";
	import swiperAction from "@/components/swiper-action/swiper-action.vue"
	moment.locale('zh-cn');
	export default {
		components: {
			swiperAction
		},
		data() {
			return {
				// 图像信息对象  
				imgDetail: {},
				// 专辑
				album: [],
				// 热评
				hot: [],
				// 评论
				comment: [],
				// 图片索引
				imgIndex:0
			}
		},
		onLoad() {
			const {
				imgIndex
			} = getApp().globalData;
			this.imgIndex=imgIndex;
			this.getData();
			const album=uni.getStorageSync('album');
			this.album=album[this.imgIndex];
		},
		methods: {
			// 给当前页面赋值
			getData() {
				const {imgList} =getApp().globalData;
				this.imgDetail = imgList[this.imgIndex];
				// 时间戳转换
				this.imgDetail.cnTime = moment(this.imgDetail.cnTime).fromNow();
				// 获取评论数据
				this.getComment();
				// this.album = getApp().globalData.album[this.imgIndex];
				
			},
			getComment() {
				this.request({
						url: "http://service.picasso.adesk.com/v2/vertical/vertical/5ab8a9c4e7bce7356a197a07/comment"
					})
					.then(res => {

						// 给hot中的对象数组添加一个时间属性
						res.res.hot.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow());
						res.res.comment.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow());
						this.hot = res.res.hot;
						this.comment = res.res.comment;
					})
			},
			// 滑动事件
			handleSwiperAction(e){
				/*  
				 1.用户 左滑 imgIndex++
				 2.用户 右滑 imgIndex--
				 3.判断数组是否越界 
				 4.左滑 e.direction==="left" && this.imgIndex<imgList.length-1
				 5.右滑 e.direction==="right" && this.imgIndex>0
				 */
				const {imgList} =getApp().globalData;
				
				if(e.direction==="left" && this.imgIndex<imgList.length-1)
				{
					// 可以左滑 加载到下一页
					this.imgIndex++;
					this.getData();
				}else if(e.direction==="right" && this.imgIndex>0){
					// 右滑 加载上一页的
					this.imgIndex--;
					this.getData();
				}else {
					uni.showToast({
						title:'没有数据啦',
						icon:"none"
					})
				}
			},
		
		  // 点击下载事件
		async	handleDownLoad(){
				// uni.downloadFile
				// uni.saveImageToPhotosAlbum
				await uni.showLoading({
					title:'下载中'
				});
				// 1.将远程文件下载到小程序的内存中
				const res1 = await uni.downloadFile({url:this.imgDetail.img})
				const {tempFilePath}=res1[1];
				
				// 2.将小程序内存中的 临时文件下载的本地中
				const res2=await uni.saveVideoToPhotosAlbum({
					filePath:tempFilePath
				});
				
				// 3.提示用户下载成功
				uni.hideLoading();
				await uni.showToast({
					title:'下载成功',
				});
				
			}
		}
		
	}
</script>

<style lang="scss">
	.user_info {
		display: flex;
		padding: 10rpx;

		.user_icon {
			padding: 0 20rpx;

			image {
				width: 88rpx;
				height: 88rpx;
				border-radius: 50%;
			}
		}

		.user_desc {
			.user_name {
				color: #000;
				font-weight: 600;

			}

			.user_time {
				color: #ccc;
				font-size: 24rpx;
				padding: 10rpx 0;
			}
		}
	}

	.user_rank {
		display: flex;
		height: 80rpx;
		border-bottom: 1rpx solid #eee;

		.rank {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}

		.user_collect {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}
	}

	.album_wrap {
		.album_title {
			padding: 20rpx;
			font-size: 34rpx;
			font-weight: 600;
		}

		.album_list {
			.album_item {
				border-bottom: 10rpx solid #eee;
				display: flex;
				padding: 10rpx 0;

				.album_cover {
					flex: 1;

					image {
						width: 180rpx;
						height: 180rpx;
					}
				}

				.album_info {
					flex: 3;
					padding-left: 20rpx;
					position: relative;

					.album_info_text {
						width: 100rpx;
						height: 50rpx;
						background-color: $color;
						color: #fff;
						display: flex;
						justify-content: center;
						align-items: center;
					}

					.album_name {
						padding: 10rpx 0;
						color: #888;
					}

					.iconfont {
						font-size: 40rpx;
						position: absolute;
						top: 50%;
						transform: translateY(-50%);
						right: 10%;
						color: #000;
					}
				}
			}
		}
	}

	// 最热评论
	.comment {
		.comment_title {
			padding: 15rpx;

			.iconfont {
				color: red;
				font-size: 40rpx;
			}

			.comment_text {
				font-weight: 600;
				font-size: 28rpx;
				color: #666;
				margin-left: 10rpx;
			}
		}

		.comment_list {
			.comment_item {
				border-bottom: 15rpx solid #ccc;

				// 用户信息
				.comment_user {
					display: flex;
					padding: 15rpx;

					.user_icon {
						width: 15%;
						display: flex;
						justify-content: center;
						align-items: center;

						image {
							width: 90%;
						}
					}

					.user_name {
						flex: 1;

						.user_nickname {
							color: #777;
						}

						.user_time {
							color: #ccc;
							font-size: 24rpx;
							padding: 5rpx;

						}
					}

					.user_badge {
						image {
							width: 40rpx;
							height: 40rpx;
							// 改成行内样式 ，可以在一行内显示
							display: inline-block;
						}

					}
				}

				// 评论数组
				.comment_desc {
					display: flex;
					padding: 30rpx 0;

					.comment_content {
						flex: 1;
						padding-left: 15%;
						color: #000;
					}

					.comment_like {
						text-align: right;

						.iconfont {}
					}
				}
			}
		}
	}

	.new {
		.iconfont {
			color: #a6e22e !important;
		}
	}
  // 下载
  .download{
		height: 120rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		.download_btn{
			width: 90%;
			height: 85%;
			background-color: $color;
			color: #fff;
			font-size: 50rpx;
			font-weight: 600;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 20rpx;
		}
	}
</style>

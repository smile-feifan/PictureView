<template>
	<view>
		<!-- 专辑背景 开始 -->
		<view class="album_bg">
			<image :src="album.cover" mode="aspectFill"></image>
			<view class="album_info">
				<view class="album_name">
					{{album.name}}
				</view>
				<view class="album_btn">
					关注专辑
				</view>
			</view>
		</view>
		<!-- 专辑背景 结束 -->
		<view class="album_author">
			<view class="album_author_info">
				<image :src="album.user.avatar" mode="widthFix"></image>
				<view class="author_name">
					{{album.user.name}}
				</view>
			</view>
			<view class="album_author_desc">
				<text>{{album.desc}}</text>
			</view>
		</view>
		<!-- 专辑作者 结束 -->
		<!-- 专辑 列表开始 -->
		<view class="album_list">
			<view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
				<go-detail :list="wallpaper" :index="index">
					<image :src="item.thumb" mode="widthFix"></image>
				</go-detail>
			</view>
		</view>
		<!-- 专辑列表结束 -->
	</view>
</template>

<script>
	import goDetail from "@/components/go-detail/go-detail.vue"
	export default {
		components: {
			goDetail
		},
		data() {
			return {
				params: {
					limit: 30,
					order: "hot",
					skip: 0,
					first: 1
				},
				id: -1,
				album: {},
				wallpaper: [],
				hasMore: true
			}
		},
		onLoad(options) {
			this.id = options.id;
			this.getList();
			this.getalbum()
		},
		// 触底加载下一页
		onReachBottom() {
			if (this.hasMore) {
				this.params.skip += this.params.limit;
				this.getalbum();
			} else {
				uni.showToast({
					title: '没有更多数据了',
					icon: 'none'
				})
			}
		},
		methods: {

			getList() {
				this.request({
						url: `http://service.picasso.adesk.com/v1/wallpaper/album/${this.id}/wallpaper`,
						data: this.params
					})
					.then(res => {
						this.album = res.res.album;
					})
			},
			getalbum() {
				this.request({
						url: 'http://service.picasso.adesk.com/v1/vertical/vertical',
						data: this.params
					})
					.then(res => {
						if (res.res.vertical.length === 0) {
							this.hasMore = false;
							uni.showToast({
								title: '没有更多数据啦',
								icon: 'none'
							})
							return;
						}
						this.wallpaper = [...this.wallpaper, ...res.res.vertical];
					})
			}
		}
	}
</script>

<style lang="scss">
	.album_bg {
		position: relative;

		image {}

		.album_info {
			padding: 0 15rpx;
			position: absolute;
			width: 100%;
			left: 0;
			bottom: 0;
			display: flex;
			justify-content: space-between;
			height: 80rpx;
			align-items: center;
			color: #fff;

			.album_name {
				font-size: 40rpx;
			}

			.album_btn {
				background-color: $color;
				width: 152rpx;
				height: 60rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				border-radius: 10rpx;
			}
		}
	}

	.album_author {
		padding: 10rpx;

		.album_author_info {
			padding: 10rpx 0;
			display: flex;

			image {
				width: 50rpx;
				// margin-right: 10rpx;
				vertical-align: middle;
			}

			.author_name {
				color: #000;
				margin-left: 15rpx;

			}
		}

		.album_author_desc {}
	}

	.album_list {
		display: flex;
		flex-wrap: wrap;

		.album_item {
			width: 33.33%;
			border: 3rpx solid #fff;

			image {}
		}
	}
</style>

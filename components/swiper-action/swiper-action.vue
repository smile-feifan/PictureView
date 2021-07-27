<template>
	<view @touchstart="handleTouchStart" @touchend="handleTouchEnd">
		<slot></slot>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 按下的时间
				startTime:0,
				// 按下的坐标
				startX:0,
				startY:0
			}
		},
		methods:{
			handleTouchStart(e){
				// console.log(e);
				// 手指按下屏幕
				// console.log("按下:"+e.changedTouches[0].clientX);
				// console.log("按下:"+e.changedTouches[0].clientY);
				
				this.startTime=Date.now();
				this.startX=e.changedTouches[0].clientX;
				this.startY=e.changedTouches[0].clientY;
			},
			handleTouchEnd(e){
				// 手指离开屏幕
				// console.log("离开:"+e.changedTouches[0].clientX);
				// console.log("离开:"+e.changedTouches[0].clientY);
				
				const endTime=Date.now();
				const endX=e.changedTouches[0].clientX;
				const endY=e.changedTouches[0].clientY;
				
				// 判断按下的时长
				if(endTime-this.startTime>2000) {
					retrun;
				}
				// 滑动的方向
				let direction="";
				
				// 先判断用户的滑动距离 是否合法  判断滑动的方向 注意。距离要加上绝对值
				if(Math.abs(endX-this.startX)>10 && Math.abs(endY-this.startY)<20){
					// 滑动的方向
					direction=endX-this.startX>0?"right":"left";
				}else {
					return;
				}
				
				// 用户做了合法操作
				// console.log(direction);
				// 向外传递事件
				this.$emit("swiperAction",{direction});
				
			}
		}
	}
</script>

<style>
</style>

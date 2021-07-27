<template>

	<view class="search">
		<view class="search_input">
			<input @input="handleInput" type="text" value="" placeholder="请输入要搜索的图片" />
		</view>
		<button size="mini" plain>搜索</button>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				textVal: ""
			}
		},
		mounted() {
		
		},
    methods:{
			handleInput(e){
				this.textVal=e.detail.value;
			}
		}
	}
</script>

<style lang="scss">
 .search{
	 display: flex;
	 padding: 30rpx 0;
	 margin: 0 10rpx;
	 .search_input{
		 flex: 8;
		 border: 1rpx solid #999;
		 border-radius: 15rpx;
		 input{}
	 }
	 button{}
 }
</style>

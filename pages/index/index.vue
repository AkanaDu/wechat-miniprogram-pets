<template>
	<view class="container">
		<view class="layout">
			<view class="box" v-for="(item, index) in pets" :key="item.id">
				<view class="pic">
					<img lazy-load :src="item.url" mode="widthFix" @click="onPreview(index)"/>
				</view>
				<view class="text">
					{{item.width}} * {{item.height}}
				</view>
			</view>
		</view>
		<view class="float">
			<view class="item" @click="onRefresh">
				刷新
			</view>
			<view class="item" @click="onTop">
				置顶
			</view>
		</view>
	</view>
</template>

<script setup>
	import {ref} from 'vue'
	import {onReachBottom, onPullDownRefresh} from '@dcloudio/uni-app'
	const pets = ref([])
	function network () {
		uni.showNavigationBarLoading()
		uni.request({
			// url: 'https://api.thecatapi.com/v1/images/search',
			url: 'https://api.thecatapi.com/v1/images/search',
			method: 'GET',
			data: {
				limit: 10
			}
		}).then(res => {
				pets.value.push(...res.data)
		}).catch(err => {
			uni.showToast({
				title: err.errMsg,
				icon: 'none'
			})
		}).finally(() => {
			uni.hideNavigationBarLoading()
			uni.stopPullDownRefresh()
		})
	}
	network()
	
	const onPreview = (index) => {
		uni.previewImage({
			current: index,
			urls: pets.value.map(o => o.url)
		})
	}
	
	onReachBottom(() => {
		network()
	})
	
	onPullDownRefresh(() => {
		pets.value = []
		network()
	})
	
	const onRefresh = () => {
		uni.startPullDownRefresh()
	}
	
	const onTop = () => {
		uni.pageScrollTo({
			scrollTop: 0,
			duration: 100
		})
	}
</script>

<style lang="scss" scoped>
	.container {
		.layout {
			padding: 50rpx;
			.box {
				margin-bottom: 60rpx;
				box-shadow: 0 10rpx 40rpx rgba(0, 0, 0, 0.08);
				border-radius: 10rpx;
				overflow: hidden;
				.pic {
					img {
						width: 100%;
					}
				}
				.text {
					padding: 30rpx;
				}
			}
		}
		.float{
			position: fixed;
			right: 30rpx;
			bottom: 80rpx;
			padding-bottom: env(safe-area-inset-bottom);
			.item {
				width: 90rpx;
				height: 90rpx;
				border-radius: 50%;
				background-color: rgba(255, 255, 255, 0.9);
				display: flex;
				justify-content: center;
				align-items: center;
				margin-bottom: 20rpx;
			}
		}
	}
</style>

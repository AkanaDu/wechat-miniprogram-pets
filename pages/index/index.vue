<template>
	<view class="container">
		<view class="menu">
			<uni-segmented-control :current="current" :values="values" @clickItem="onClickItem" styleType="button" activeColor="#2b9939"></uni-segmented-control>
		</view>
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
				<uni-icons type="refreshempty" size="26" color="#888"></uni-icons>
			</view>
			<view class="item" @click="onTop">
				<uni-icons type="arrow-up" size="26" color="#888"></uni-icons>
			</view>
		</view>
		<view class="load-more">
			<uni-load-more status="loading"></uni-load-more>
		</view>
	</view>
</template>

<script setup>
	import {ref, computed} from 'vue'
	import {onReachBottom, onPullDownRefresh} from '@dcloudio/uni-app'
	const pets = ref([])
	const current = ref(0)

	const classify = [
		{
			key: 'all',
			value: '全部'
		}, 
		{
			key: 'dog',
			value: '汪星人'
		},
		{
			key: 'cat',
			value: '喵星人'
		}
	]
	const values = computed(() => classify.map(o => o.value))
	const onClickItem = (e) => {
		current.value = e.currentIndex
	}
	function network () {
		uni.showNavigationBarLoading()
		uni.request({
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
		.menu {
			padding: 50rpx 50rpx 0 50rpx;
		}
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
		.load-more {
			padding-bottom: calc(env(safe-area-inset-bottom) + 50rpx);
		}
	}
</style>

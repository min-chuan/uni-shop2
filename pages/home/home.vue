<template>
	<view>
		<!-- 轮播图的区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="item.goods_id">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航区域 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
				<image class="nav-img" :src="item.image_src"></image>
			</view>
		</view>
		<!-- 楼层区域 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item, i) in floorList" :key="i">
				<image class="floor-title" :src="item.floor_title.image_src"></image>
				<view class="floor-img-box">
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image mode="widthFix" :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"></image>
					</navigator>
					<view class="right-img-box">
						<navigator :url="item2.url" class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2">
							<image v-if="i2 !== 0" mode="widthFix" :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}"></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperList: [],
				navList: [],
				floorList: []
			};
		},
		onLoad(){
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods: {
			async getSwiperList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
				if(res.meta.status !== 200){
					return uni.$showMsg()
				}
				this.swiperList = res.message
			},
			async getNavList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/catitems')
				if(res.meta.status !== 200){
					return uni.$showMsg()
				}
				this.navList = res.message
			},
			async getFloorList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/floordata')
				if(res.meta.status !== 200){
					return uni.$showMsg()
				}
				res.message.forEach(floor => {
					floor.product_list.forEach(product => {
						product.url = '/subpkg/goods_list/goods_list?' + product.navigator_url.split('?')[1] 
					})
				})
				this.floorList = res.message
			},
			navClickHandler(item){
				if(item.name === '分类'){
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			}
		}
	}
</script>

<style lang="scss">
swiper {
	height: 330rpx;
	.swiper-item,
	image {
		height: 100%;
		width: 100%;
	}
}
.nav-list {
	display: flex;
	justify-content: space-between;
	margin: 15rpx 0;
	.nav-img {
		width: 128rpx;
		height: 140rpx;
	}
}
.floor-list {
	.floor-title {
		width: 100%;
		height: 60rpx;
	}
	.floor-img-box {
		display: flex;
		padding-left: 10rpx;
	}
	.right-img-box {
		display: flex;
		justify-content: space-around;
		flex-wrap: wrap;
	}
}
</style>

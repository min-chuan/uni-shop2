<template>
	<view>
		<view class="scroll-view-container">
			<!-- 左侧的滑动区 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh + 'px'}">
				<view v-for="(item, i) in catList" :class="['left-scroll-view-item', i === active
				 ? 'active' : '']" @click="acitiveChanged(i)">{{item.cat_name}}</view>
			</scroll-view>
			<!-- 右侧的滑动区 -->
			<scroll-view class="right-scroll-view" :scrollTop="scrollTop" scroll-y="true" :style="{height: wh + 'px'}">
				<view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
					<view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
					<view class="cate-lv3-list">
						<view class="cate-lv3-item" @click="gotoGoodsList(item3)" v-for="(item3, i3) in item2.children" :key="i3">
							<image :src="item3.cat_icon" @error="imageError($event, item3, i3)" ></image>
							<text>{{item3.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh: 0,
				catList: [],
				active: 0,
				cateLevel2: [],
				scrollTop: 0
			}
		},
		onLoad(){
			const sysInfo = uni.getSystemInfoSync()
			this.wh = sysInfo.windowHeight;
			this.getCatList()
		},
		methods: {
			async getCatList(){
				const {data: res} = await uni.$http.get('/api/public/v1/categories')
				if(res.meta.status !== 200){
					return uni.$showMsg()
				}
				this.catList = res.message;
				this.cateLevel2 = this.catList[0].children
			},
			acitiveChanged(i){
				this.active = i;
				this.scrollTop = this.scrollTop ? 0 : 1;
				this.cateLevel2 = this.catList[i].children
			},
			imageError(e, item, index){
				console.log('失败')
				item[index].cat_name = '/static/logo.png'
			},
			// 跳转到商品列表页面
			gotoGoodsList(item){
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id
				})
			}
		}
	}
</script>

<style lang="scss">
.scroll-view-container {
	display: flex;
	.left-scroll-view {
		width: 120px;
		.left-scroll-view-item {
			background-color: #f7f7f7;
			line-height: 60px;
			text-align: center;
			font-size: 12px;
			&.active {
				position: relative;
				background-color: #fff;
				&::before {
					content: ' ';
					position: absolute;
					top: 50%;
					transform: translateY(-50%);
					display: block;
					width: 3px;
					height: 30px;
					background-color: #c00000;
				}
			}
		}
	}
	.right-scroll-view {
		.cate-lv2-title {
			font-size: 12px;
			font-weight: bold;
			text-align: center;
			padding: 15px 0;
		}
		.cate-lv3-list {
			display: flex;
			flex-wrap: wrap;
			.cate-lv3-item {
				width: 33.33%;
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				margin-bottom: 10px;
				
				image {
					width: 60px;
					height: 60px;
					border: 1px solid #000;
				}
				
				text {
					font-size: 12px;
				}
			}
		}
	}
}
</style>

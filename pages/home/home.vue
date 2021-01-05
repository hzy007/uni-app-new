<template>
	<view>
		<!-- 轮播图 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in swiperList" v-key="i">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src" ></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,index) in navlist" :key="index" @click="navItem(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>
		<!-- 楼层 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item,index) in floorList" :key="index">
				<!-- 标题 -->
				<view class="">
					<image :src="item.floor_title.image_src" class="floor-title"></image>
				</view>
				<!-- 楼层图片区域 -->
				<view class="floor-img-box">
					<!-- 左侧大图片 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx' }" mode="widthFix"></image>
					</navigator>
					<!-- 右侧小图片 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2,index2) in item.product_list" :key="index2" v-if="index2 !==0" :url="item2.url">
							<image :src="item2.image_src" :style="{width:item2.image_width + 'rpx' }" mode="widthFix"></image>
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
				// 轮播图数据
				swiperList:[],
				// 分类导航数据列表
				navlist:[],
				// 楼层数据
				floorList:[]
			};
		},
		onLoad() {
			this.getSwiperList(),
			this.getNavList(),
			this.getFollrList()
		},
			
		methods:{
			// 轮播图请求数据
            async getSwiperList(){
			const {data :res} = await uni.$http.get('/api/public/v1/home/swiperdata')
			if(res.meta.status !==200){
				return uni.$showMsg()
			}
			this.swiperList=res.message
			},
			// 分类导航请求数据
			async getNavList(){
			const {data:res} =  await uni.$http.get('/api/public/v1/home/catitems')
			// console.log(res);
			if(res.meta.status !==200)  return uni.$showMsg()
			this.navlist=res.message
			},
			// 分类导航点击事件
			navItem(item){
				// console.log(item);
				if(item.name === '分类'){
					// console.log(123);
					uni.switchTab({
						url:'/pages/cata/cata'
					})
				}
			},
			// 获取楼层数据	
			async getFollrList(){
				const {data:res} =  await uni.$http.get('/api/public/v1/home/floordata')
				console.log(res);
				if(res.meta.status !==200)  return uni.$showMsg()
				res.message.forEach(i => {
					i.product_list.forEach(j => {
						j.url='/subpkg/goods_list/goods_list?' + j.navigator_url.split('?')[1]
					})
				})
				this.floorList=res.message
			}
		}
	}	
</script>

<style lang="scss">
swiper{
	height: 330rpx;
	.swiper-item,image{
		width: 100%;
		height: 100%;
	}
}
	
.nav-list{
	display: flex;
	justify-content: space-around;
	margin: 15px 0;	
	.nav-img{
		width: 128rpx;
		height: 140rpx;
	}
}
.floor-img-box{
	display: flex;
}	
.floor-title{
	width: 100%;
	height: 60rpx;
}
	
.right-img-box{
	display: flex;
	flex-wrap: wrap;
}
</style>

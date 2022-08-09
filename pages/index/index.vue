<template>
	<view class="root">
		<u-icon name="man"></u-icon>
		<!-- <view class="header">
			<view class="search-bar">
				<view class="search-bar-box">
					<view class="position">
						<image src="../../static/position.png"></image>
						<text>海陵...</text>
					</view>
					<view class="arrow" style="margin-top: 12px;"></view>
					<input type="text" disabled="true" class="search-text" placeholder="请输入搜索内容" />
					<button class="search-btn" @click="search">搜索</button>
				</view>
			</view>
		</view>
		<view class="container">
			<view class="top-menu">
				<view v-for="(item,index) of topMenu" class="td">
					<image src="../../static/logo.png"></image>
					<text>{{item}}</text>
				</view>
			</view>
			<swiper circular :indicator-dots="true">
				<swiper-item v-for="(item,index) of bannerUrl">
					<image :src="item.imageUrl"></image>
				</swiper-item>
			</swiper>
			<swiper circular :indicator-dots="true" style="height: 25vh;margin-top: 4vh;">
				<swiper-item class="main-menu">
					<view class="td" v-for="(item,index) of mainMenu">
						<image :src="item.iconUrl"></image>
						<text>{{item.categoryName}}</text>
					</view>
				</swiper-item>
				
			</swiper>
			<view class="seckill">
				<view class="hot">
					限时秒杀
					<image src="../../static/hot.png"></image>
				</view>
				<view class="datasets">
					<view class="spacing">
						<view class="item" v-for="(item,index) of mainMenu[0]">
							<image src="../../static/logo.png"></image>
							<view>
								<text style="font-size: 1em;">秒杀进行中</text>
								<text style="font-weight: bold;">白桃乌龙茶</text>
								<text style="font-weight: bold;color: red; margin:8% 0%">10积分</text>
								<view style="background-color: red; color:white;text-align: center;">立即秒杀</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="nav">
				<picker class="item" @columnchange="restaurantChange" mode="multiSelector" :range="restaurant" :value="restIndex">
					<text>{{restaurant[0][restIndex[0]]}}/{{restaurant[1][restIndex[1]]}}</text>
					<view class="arrow" style="margin-left: 4px;vertical-align: sub;"></view>
				</picker>
				<picker class="item" @change="comprehensiveChange" :range="comprehensive" :value="comprehensiveIndex">
					<text>{{comprehensive[comprehensiveIndex]}}</text>
					<view class="arrow" style="margin-left: 4px;vertical-align: sub;"></view>
				</picker>
				<picker class="item" @change="priceChange" :range="price" :value="priceIndex">
					<text>{{price[priceIndex]}}</text>
					<view class="arrow" style="margin-left: 4px;vertical-align: sub;"></view>
				</picker>
			</view>
			<view class="list-view">
				
				<view class="item" v-for="(item,index) of goods">
					<image :src="item.smallPic"></image>
					<view class="text">
						<text style="background-color: aliceblue;padding: 1px 4px;">{{item.merchantName}} ></text>
						<label style="font-weight: bold;display: block;margin: 1px 0;">{{item.goodName}}</label>
						<view>
							<label style="color: red;">{{item.integralPrice}}积分</label>
							<label style="margin-left: 3%;text-decoration: line-through;">¥{{item.showPrice}}</label>
							<view style="margin-left: -10%;"><label style="font-size: 10px;display: block;-webkit-transform: scale(0.8)">{{item.category1Name}}</label>
			</view>
						</view>
					</view>
					<view class="distance">
						<view>{{item.distance}}</view>
						<view class="exchange">立即兑换</view>
					</view>
				</view>
			</view>
		</view> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				topMenu: [
					"激活积分",
					"会员任务",
					"积分活动"
				],
				mainMenu: [
					/* ["美食", "购物", "丽人", "休闲娱乐", "火锅/烧烤", "甜点饮品", "水果", "医药", "美发", "洗车/美容"],
					["住宿", "教育/培训", "生活服务", "宠物", "家居装修", "汽车", "亲子乐园", "其他"] */
				],
				restaurant:[
					["美食","购物"],
					["酒楼","火锅/烧烤"]
				],
				comprehensive:[
					"综合排序",
					"销量最高",
					"距离最近",
					"好评优先"
				],
				price:[
					"全部",
					"1-500积分",
					"500-2000积分",
					"2000积分+"
				],
				bannerUrl:[],
				goods:[],
				restIndex:[0,0],
				comprehensiveIndex:0,
				priceIndex:0
			
			}
		},
		onLoad() {
			console.log(uni.$u.config.v);
			uni.request({
				url:"https://api.dev.hztongbei.com/app/common/getBannerEventList",
				method:"GET",
				success:res=>{
					var data = res.data["data"];
					this.bannerUrl = data;
				}
			});
			uni.request({
				url:"https://api.dev.hztongbei.com/app/merchant/category/merchantCategoryIndex",
				method:"GET",
				success:res=>{
					var menuCategory = res.data["data"];
					this.mainMenu = menuCategory;
				}
			})
			uni.request({
				url:"https://api.dev.hztongbei.com/app/merchant/good/getMerchantGoodListNearBy?merchantType=2&current=1",
				method:"GET",
				success: res => {
					var records = res.data["data"]["records"];
					this.goods = records;
				}
			});
		},
		methods: {
			restaurantChange:function(e){
				this.restIndex[e.detail.column] = e.detail.value;
				switch(e.detail.column){
					case 0:
						switch(this.restIndex[0]){
							case 0:
								this.restaurant[1] = ["酒楼","火锅/烧烤"];
								break;
							case 1:
								this.restaurant[1] = ["超市/便利店","零食干货/特产专卖"];
								break;
						}
						this.restIndex.splice(1,1,0);
						this.restIndex.splice(2,1,0);
						break;
				}
				this.$forceUpdate();
			},
			comprehensiveChange:function(e){
				this.comprehensiveIndex = e.detail.value;
			},
			priceChange:function(e){
				this.priceIndex = e.detail.value;
			},
			search:function(){
				console.log("update...");
			}
		}
	}
</script>

<style>
	.header {
		text-align: left;
		position: fixed;
		top: 0;
		z-index: 1;
		position: sticky;
		background-color: wheat;
	}

	.container {
		background-color: aliceblue;
		overflow-y: scroll;
		height: 89vh;
		overflow-x: hidden;
	}

	.top-menu {
		background-color: wheat;
		padding: 3vh 0;
	}

	.main-menu {
		padding-bottom: 3vh;
		text-align: left;
	}

	.search-bar {
		height: 45px;
	}

	.search-bar-box {
		display: flex;
		height: 35rpx;
		border: 5rpx solid wheat;
		border-radius: 14rpx;
	}

	.position {
		width: 20%;
		line-height: 35rpx;
		margin-top: 15rpx;
	}

	.position>text {
		font-size: 16px;
		margin-left: 10%;
	}

	.position>image {
		width: 20px;
		height: 20px;
		vertical-align: text-top;
	}

	.arrow {
		width: 0;
		height: 0;
		border: 5px solid;
		display: inline-block;
		border-color: black transparent transparent transparent;
		margin-top: 12px;
	}

	.search-text {
		width: 40%;
		margin-top: 10rpx;
		margin-left: 20rpx;
		font-size: 30rpx;
		color: #7f7f81;
		background-color: white;
		padding: 1% 2%;
		border-radius: 14rpx;
		line-height: 35rpx;
		height: 35rpx;
	}

	.search-btn {
		background-color: #cc5600;
		/* Green */
		color: white;
		text-align: center;
		display: inline-block;
		height: 58rpx;
		line-height: 55rpx;
		border-top-left-radius: 0px;
		border-bottom-left-radius: 0px;
		margin-top: 5px;
		margin-left: -2%;
	}

	.top-menu>.td {
		justify-content: center;
		text-align: center;
		width: 33%;
		display: inline-block;
	}

	.main-menu>.td {
		width: 20%;
		justify-content: center;
		text-align: center;
		display: inline-block;
		margin-bottom: 4%;
	}

	.top-menu>.td>image,
	.main-menu>.td>image {
		width: 50px;
		height: 50px;
	}

	.top-menu>.td>text,
	.main-menu>.td>text {
		display: block;
		font-size: 12px;
	}

	swiper {
		width: 96%;
		text-align: center;
		margin: 2% auto;
	}

	swiper-item>image {
		width: 100%;
		border-radius: 10px;
		height: 20vh;
	}

	.seckill {
		width: 92%;
		margin: 0 auto;
	}

	.hot>image {
		width: 12px;
		height: 12px;
	}

	.datasets {
		background-color: white;
		margin: 0 auto;
		padding: 2% 4%;
	}

	.spacing::-webkit-scrollbar {
		display: none;
	}
	
	.spacing{
		overflow-x: scroll;
		white-space: nowrap;
	}

	.spacing>.item {
		background-color: white;
		padding: 2%;
		width: 60%;
		display: contents;
	}

	.spacing>.item>image {
		width: 100px;
		height: 100px;
		margin-right: 2%;
		vertical-align: text-bottom;
	}

	.spacing>.item>view>text {
		display: block;
	}

	.spacing>.item>view {
		display: inline-block;
		padding-right: 4%;
		margin-right: 2%;
		border-right: 1px solid #ccc;
	}

	.list-view {
		width: 92%;
		margin: 10px auto;
	}

	.list-view>.item {
		width: 92%;
		background-color: white;
		margin-bottom: 1vh;
		padding: 2% 4%;
		border-radius: 4px;
	}

	.list-view>.item>image {
		width: 60px;
		height: 60px;
	}

	.text {
		display: inline-block;
		margin-left: 2%;
		vertical-align: top;
	}

	.distance {
		float: right;
		margin-top: 5%;
		text-align: right;
	}

	.exchange {
		background-color: #cc5600;
		color: white;
		padding: 4px 8px;
		margin-top: 20%;
		border-radius: 4px;
		font-size: 12px;
	}
	
	.nav{
		margin: 2vh 0;
		width: 100%;
		padding: 0% 4%;
	}
	
	.nav > .item{
		display: inline-block;
		margin-right: 8%;
		font-weight: bold;
	}
</style>

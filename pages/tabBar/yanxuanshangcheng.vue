<template>
	<view class="index">
		<view class="nav1">
			<view class="tit1">
				<view class="myInp" @click="toSearch">
					<u-icon name="search" color="#000000" size="28"></u-icon>
					<input type="text" class="input" placeholder='搜索专家/项目/关键词' placeholder-style='color: #122106;' />
				</view>
				<view class="service">
					<button open-type="contact" class="u-reset-button">
						<image src="/static/image/zu1525.png" class="fixed-icon2" mode=""></image>
					</button>
				</view>
			</view>
			<view class="tit3">
				<u-swiper @click='lbtClick' height='252' :list="bannerList"></u-swiper>
			</view>
		</view>
		<view class="nav2">
			<view class="items">
				<view class="item" @click="toSearchResult(35)">
					<image src="/static/image/tu01.png" class="pic-item" mode=""></image>
					<view class="txt-item">美容护肤</view>
				</view>
				<view class="item" @click="toSearchResult(36)">
					<image src="/static/image/tu02.png" class="pic-item" mode=""></image>
					<view class="txt-item">光电美肤</view>
				</view>
				<view class="item" @click="toSearchResult(38)">
					<image src="/static/image/tu03.png" class="pic-item" mode=""></image>
					<view class="txt-item">注射美肤</view>
				</view>
				<view class="item" @click="toSearchResult(39)">
					<image src="/static/image/tu04.png" class="pic-item" mode=""></image>
					<view class="txt-item">个护清洁</view>
				</view>
			</view>
			<view class="items items2">
				<view class="item" @click="toSearchResult(40)">
					<image src="/static/image/tu05.png" class="pic-item" mode=""></image>
					<view class="txt-item">彩妆</view>
				</view>
				<view class="item" @click="toSearchResult(41)">
					<image src="/static/image/tu06.png" class="pic-item" mode=""></image>
					<view class="txt-item">香水</view>
				</view>
				<view class="item" @click="toSearchResult(42)">
					<image src="/static/image/tu07.png" class="pic-item" mode=""></image>
					<view class="txt-item">轻奢</view>
				</view>
				<view class="item" @click="toSearchResult(43)">
					<image src="/static/image/tu08.png" class="pic-item" mode=""></image>
					<view class="txt-item">其他</view>
				</view>
			</view>
		</view>
		<view class="nav3 nav4">
			<view class="title">
				<view class="t-txt">热门推荐</view>
			</view>
			<scroll-view class="scroll-view" scroll-x style="width: 100%;white-space:nowrap;">
				<view class="itemss">
					<view class="item" @click="toJifenchanpin">
						<image src="/static/image/mcz23.png" class="pic" mode=""></image>
						<view class="txt1">积分兑换</view>
						<view class="txt2">星品限时1086积分</view>
						<view class="btn">点击进入 >></view>
					</view>
					<view class="item2">
						<view class="txt1">每日特惠</view>
						<view class="i2-items2">
							<view class="i2-item" @click="toRexiaoxiangqin(item)" v-for="item in dijiaList" :key='item.id'>
								<image :src="item.image" class="item2-pic" mode="aspectFill"></image>
								<view class="btn">¥{{item.price}}</view>
							</view>
						</view>
					</view>
					<view class="item item3">
						<image src="/static/image/zu1897.png" class="pic" mode=""></image>
						<view class="txt1">低价拼团</view>
						<view class="txt2">专属拼团价</view>
						<view class="i2-items2">
							<view class="i2-item" @click="toDijiapintuan(item.id)" v-for="item in pintuanList" :key='item.id'>
								<image :src="item.image" class="item2-pic" mode="aspectFill"></image>
								<view class="btns">¥{{item.price}}</view>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<!-- <view class="nav5">
			<u-tabs-swiper bg-color="#F7F8FA" height='96' font-size="28" gutter="30" inactive-color="#707070"
				bar-height="4" bar-width="64" active-color="#BD9E81" ref="uTabs" :list="list" :current="current"
				@change="tabsChange" :is-scroll="false" swiperWidth="750"></u-tabs-swiper>
			<swiper :style="[{height: height + 'px'}]" :current="swiperCurrent" :vertical='false'
				 @animationfinish="animationfinish">
				<swiper-item class="swiper-item" v-for="(item, index) in list" :key="index">
					<scroll-view scroll-y='true'>
						<view class="nav5Items">
							<view class="item" @click="goPro(item)" v-for="item in shopList" :key='item.product_id'>
								<image :src="item.image" class="pic" mode=""></image>
								<view class="right">
									<view class="up">
										<view class="txt1">{{item.store_name}}</view>
										<view class="txt2">{{item.store_info}}</view>
									</view>
									<view class="down">
										<view class="d-t1">￥{{item.price}}</view>
										<image src="/static/image/zu1521.png" class="d-pic" mode=""></image>
									</view>
								</view>
							</view>
							<u-loadmore v-if='swiperCurrent == index' :status="status" font-size="22" />
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view> -->
		<view class="tabmenu_sticky">
			<view class="tabmenu">
				<view class="tabitem" :class="{'active':current==index}" v-for="(item,index) in list" :key="index" @click="tabsChange(index)">{{item.name}}</view>
			</view>
		</view>
		<view class="nav5Items">
			<view class="item" @click="goPro(item)" v-for="item in shopList" :key='item.product_id'>
				<image :src="item.image" class="pic" mode=""></image>
				<view class="right">
					<view class="up">
						<view class="txt1">{{item.store_name}}</view>
						<view class="txt2">{{item.store_info}}</view>
					</view>
					<view class="down">
						<view class="d-t1">￥{{item.price}}</view>
						<image @click.stop="addGwc(item)" src="/static/image/zu1521.png" class="d-pic" mode=""></image>
					</view>
				</view>
			</view>
		</view>
		<u-loadmore :status="status" font-size="22" bg-color="#fff" />
		<view class="cart" @click="goCart">
			<view class="pic">
				<image src="/static/image/zu1842.png" class="fixed-icon" mode=""></image>
				<u-badge type="error" :count="cartCount" :offset="[30,30]"></u-badge>
			</view>
		</view>
	</view>
</template>

<script>
	import {mapState} from "vuex";
	export default {
		data() {
			return {
				cartCount:0,
				noChange:false,
				dijiaList:[],
				pintuanList:[],
				shopList: [],
				searchVal: '',
				bannerList: [],
				imgArr: [],
				//
				swiperCurrentIndex: 0,
				height: 0,
				list: [{
					name: '热销'
				}, {
					name: '推荐'
				}, {
					name: '口碑'
				}, {
					name: '精选'
				}],
				// 因为内部的滑动机制限制，请将tabs组件和swiper组件的current用不同变量赋值
				current: 0, // tabs组件的current值，表示当前活动的tab选项
				swiperCurrent: 0, // swiper组件的current值，表示当前那个swiper-item是活动的
				// 加载
				status: 'loadmore',
				page:1
			}
		},
		onLoad(options) {
			this.getBanner();
			this.tabsChange(this.current);
		},
		onReachBottom() {
			console.log(2);
			this.getData();
		},
		onPullDownRefresh() {
			this.getBanner();
			this.getData2();
			this.getCartNum();
			this.reloadData();
			uni.stopPullDownRefresh();
		},
		onShow() {
			this.getData2();
			this.getCartNum();
		},
		// mounted() {
		// 	setTimeout(()=>{
		// 		this.getCurrentSwiperHeight('.nav5Items')
		// 	},900)
		// },
		methods: {
			async addGwc(item){
				console.log(item)
				const res = await this.$api.cartAdd({
					productId: item.product_id,
					cartNum: 1,
					uniqueId: item.sku.unique,
					new:0
				})
				if(res.status==200){
					this.cartCount ++;
				}
			},
			async getData2(){
				const res = await this.$api.yanxuan_index({
					page:1,
					limit:2
				})
				this.dijiaList = res.data.day;
				this.pintuanList = res.data.tuan;
			},
			async getBanner(){
				const res = await this.$api.banner({
					position:0
				})
				this.bannerList = res.data;
				this.bannerList.forEach(ele=>{
					ele.image = ele.image_url;
				})
			},
			async getCartNum(){
				await this.$api.getCartCounts().then(res => {
					if(res.status==200){
						this.cartCount = res.data.count;
						this.$store.commit('setCartNum', res.data.count > 99 ? '..' : res.data.count);
					}
				});
			},
			lbtClick(i){
				if(this.bannerList[i].type == 1){
					// 严选
					uni.navigateTo({
						url:`/pages/yanxuanshangcheng/rexiaoxiangqin/rexiaoxiangqin?id=${this.bannerList[i].redirect_url}`
					})
				}else{
					// 首页
					uni.navigateTo({
						url:`/pages/index/search/xiangqin?id=${this.bannerList[i].redirect_url}`
					})
				}
			},
			async getData() {
				this.status = 'loading';
				setTimeout(async () => {
					const res = await this.$api.productHot({
						page: this.page,
						limit: 10,
						product_from:0,
						is_best:this.current == 3 ? 1 : '',
						is_good:this.current == 1 ? 1 : '',
						is_comment:this.current == 2 ? 1 : '',
					})
					if(res.status==200){
						if(res.data.length==0){
							this.status = 'nomore';
							return false;
						}
						if(this.page!=1){
							this.shopList = this.shopList.concat(res.data);
						}else{
							if(res.data.length<10){
								this.status = 'nomore';
							}
							this.shopList = res.data;
						}
						this.page ++;
					}
				}, 200)
			},
			reloadData(){
				this.shopList = [];
				this.page = 1;
				this.getData();
			},
			toSearchResult(val) {
				uni.navigateTo({
					url: `/pages/search/searchResult?sid=${val}`
				})
			},
			toRexiaoxiangqin(item) {
				uni.navigateTo({
					url: `/pages/yanxuanshangcheng/rexiaoxiangqin/rexiaoxiangqin?id=${item.id}`
				})
			},
			goPro(item) {
				uni.navigateTo({
					url: `/pages/yanxuanshangcheng/rexiaoxiangqin/rexiaoxiangqin?id=${item.product_id}`
				})
			},
			toJifenchanpin() {
				uni.navigateTo({
					url: '/pages/yanxuanshangcheng/jifenchanpin/liebiao'
				})
			},
			toDijiapintuan(id) {
				uni.navigateTo({
					url: `/pages/yanxuanshangcheng/dijiapintuan/dijiapintuan?id=${id}`
				})
			},
			toZhuanjiatuandui() {
				uni.navigateTo({
					url: '/pages/index/zhuanjiatuandui/zhuanjiatuandui'
				})
			},
			toXiufu(i) {
				uni.navigateTo({
					url: `/pages/index/xiufu/xiufu?index=${i}`
				})
			},
			toSearch() {
				uni.navigateTo({
					url: '/pages/search/search?type=0'
				})
			},
			// tabs通知swiper切换
			tabsChange(index) {
				this.swiperCurrent = index;
				this.swiperCurrentIndex = index;
				this.current = index;
				this.reloadData();
			},
			// swiper-item左右移动，通知tabs的滑块跟随移动
			transition(e) {
				let dx = e.detail.dx;
				this.$refs.uTabs.setDx(dx);
			},
			animationfinish(e) {
				let current = e.detail.current;
				this.$refs.uTabs.setFinishCurrent(current);
				this.swiperCurrent = current;
				this.current = current;
				// this.tabsChange(this.current)
			},
			getCurrentSwiperHeight(element) {
				let query = uni.createSelectorQuery().in(this);
				query.selectAll(element).boundingClientRect();
				query.exec((res) => {
					this.height = res[0][this.swiperCurrentIndex].height ;
				})
			},
			goCart(){
				uni.navigateTo({
					url:"/pages/cart/index"
				})
			}
		}
	}
</script>

<style>
	page {
		background: #F7F8FA;
	}
</style>
<style lang="scss" scoped>
	/deep/ .u-load-more-wrap {
		// width: 686rpx;
		height: 100rpx !important;
	}

	.cart {
		position: fixed;
		bottom: 10rpx;
		right: 10rpx;
		z-index: 99;
		.pic{
			position: relative;
			.fixed-icon {
				width: 172rpx;
				height: 172rpx;
			}
		}
	}
	.service{
		z-index: 99;
		position: absolute;
		width: 60rpx;
		height: 60rpx;
		right: 24rpx;
		top: 22rpx;
		.fixed-icon2 {
			width: 60rpx;
			height: 60rpx;
		}
	}

	.nav1 {
		background: url(../../static/image/juxin.png) no-repeat;
		background-size: 100% 100%;
		width: 750rpx;
		height: 300rpx;
		background-color: #FFFFFF;
		// background: linear-gradient(316deg, #F6A309 0%, #F8B607 22%, #FFD438 42%, #F9C706 51%, #F8CA20 80%, #FECF05 100%);
		padding: 0 24rpx;

		.tit1 {
			padding-top: 20rpx;
			display: flex;
			align-items: center;

			.txt1 {
				font-size: 28rpx;
				font-weight: 500;
				line-height: 40rpx;
				color: #122106;
				margin-right: 14rpx;
			}

			.myInp {
				padding: 0 24rpx;
				width: 622rpx;
				height: 60rpx;
				background: rgba(255, 255, 255, 0.2);
				border-radius: 50rpx;
				display: flex;
				align-items: center;

				.input {
					width: 540rpx;
					font-size: 24rpx;
					font-weight: 400;
					line-height: 60rpx;
					color: #122106;
					text-align:center;
				}
			}
		}

		.tit3 {
			margin-top: 24rpx;
		}

	}

	.nav2 {
		background: #FFFFFF;
		padding: 108rpx 38rpx 0 38rpx;

		.items {
			display: flex;
			align-items: center;
			justify-content: space-between;

			.item {
				width: 20%;
				display: flex;
				flex-direction: column;
				align-items: center;

				.pic-item {
					width: 76rpx;
					height: 76rpx;
				}

				.txt-item {
					margin-top: 32rpx;
					font-size: 28rpx;
					font-weight: 500;
					line-height: 40rpx;
					color: #BD9E81;
				}
			}
		}

		.items2.items {
			margin-top: 40rpx;
			padding-bottom: 40rpx;
		}

		.nav2-1 {
			margin-top: 40rpx;
			padding-bottom: 40rpx;
			display: flex;
			align-items: center;
			justify-content: space-between;

			.pic3-1 {
				width: 28rpx;
				height: 28rpx;
				transform: translateX(16rpx);
				margin-right: 16rpx;
			}

			.tit3-1 {
				padding-left: 24rpx;
				width: 612rpx;
				height: 60rpx;
				background: #fff2ee;
				border-radius: 30rpx;
				font-size: 24rpx;
				font-weight: 500;
				color: #FF7C5E;
				line-height: 60rpx;
			}
		}
	}

	.nav3 {
		margin-top: 20rpx;
		width: 750rpx;
		background: #FFFFFF;
		padding: 28rpx 0;

		.title {
			display: flex;
			align-items: center;

			.shu {
				margin-left: 32rpx;
				margin-right: 20rpx;
				width: 6rpx;
				height: 28rpx;
				background: #BD9E81;
			}

			.t-txt {
				margin-left: 24rpx;
				font-size: 32rpx;
				font-weight: 500;
				color: #BD9E81;
			}
		}

		.items {
			padding: 0 40rpx;
			margin-top: 36rpx;
			display: flex;
			align-items: center;

			.item {
				width: 25%;
				display: flex;
				flex-direction: column;
				align-items: center;

				.pic {
					width: 150rpx;
					height: 150rpx;
					border-radius: 10rpx;
				}

				.txt {
					margin-top: 12rpx;
					font-size: 28rpx;
					font-weight: 400;
					color: #BD9E81;
				}
			}
		}
	}

	.nav4.nav3 {

		.scroll-view {
			height: 272rpx;
		}

		.itemss {
			margin-top: 36rpx;
			display: flex;
			align-items: center;

			.item {
				position: relative;
				margin-left: 24rpx;
				display: flex;
				flex-wrap: nowrap;
				align-items: center;
				flex-shrink: 0;
				height: 224rpx;
				width: 318rpx;

				&:last-child {
					width: 342rpx;
				}

				.pic {
					position: absolute;
					top: 0;
					left: 0;
					height: 224rpx;
					width: 318rpx;
				}

				.txt1 {
					position: absolute;
					top: 16rpx;
					left: 20rpx;
					font-size: 32rpx;
					font-weight: bold;
					color: #FFFFFF;
				}

				.txt2 {
					position: absolute;
					top: 64rpx;
					left: 20rpx;
					font-size: 24rpx;
					font-weight: 400;
					color: #FFFFFF;
				}

				.btn {
					position: absolute;
					top: 170rpx;
					left: 20rpx;
					width: 140rpx;
					height: 36rpx;
					background: #FFFFFF;
					border-radius: 40rpx;
					font-size: 20rpx;
					font-weight: 800;
					line-height: 36rpx;
					text-align: center;
					color: #C365DB;
				}
			}

			.item2 {
				padding: 0 28rpx;
				margin-left: 24rpx;
				width: 318rpx;
				height: 226rpx;
				background: #FFFFFF;
				box-shadow: 0rpx 0rpx 6rpx rgba(0, 0, 0, 0.06);
				border-radius: 10rpx;

				.txt1 {
					margin-bottom: 10rpx;
					margin-top: 16rpx;
					font-size: 28rpx;
					font-weight: bold;
					color: #BD9E81;
				}

				.i2-items2 {
					display: flex;
					align-items: center;

					.i2-item {
						&:nth-child(1) {
							margin-right: 22rpx;
						}
						.item2-pic {
							width: 120rpx;
							height: 100rpx;
							background-color: #eee;
							border-radius: 8rpx;
						}

						.btn {
							margin-top: 10rpx;
							width: 120rpx;
							height: 30rpx;
							background: rgba(250, 134, 119, 0.1);
							border-radius: 40rpx;
							font-size: 24rpx;
							font-weight: bold;
							line-height: 30rpx;
							text-align: center;
							color: #FA8677;
						}
					}
				}
			}

			.item3.item {
				width: 336rpx;
				height: 242rpx;
				margin-left: 18rpx;

				.pic {
					width: 336rpx;
					height: 242rpx;
				}

				.txt1 {
					position: absolute;
					top: 24rpx;
					left: 36rpx;
					font-size: 28rpx;
					font-family: PingFang SC;
					font-weight: bold;
					line-height: 40rpx;
					color: #FFFFFF;
				}

				.txt2 {
					position: absolute;
					top: 32rpx;
					left: 156rpx;
					font-size: 20rpx;
					font-family: PingFang SC;
					font-weight: 400;
					line-height: 28rpx;
					color: #FFFFFF;
				}

				.i2-items2 {
					position: relative;
					left: 36rpx;
					top: 20rpx;
					display: flex;
					align-items: center;

					.i2-item {
						&:nth-child(1) {
							margin-right: 22rpx;
						}

						.item2-pic {
							width: 120rpx;
							height: 100rpx;
							background-color: #eee;
							border-radius: 8rpx;
						}

						.btns {
							margin-top: 10rpx;
							width: 120rpx;
							height: 30rpx;
							background: #FFFFFF;
							border-radius: 40rpx;
							font-size: 24rpx;
							font-weight: bold;
							line-height: 30rpx;
							text-align: center;
							color: #FA8677;
						}

					}

				}
			}
		}

		.picc {
			width: 702rpx;
			height: 8rpx;
			margin-left: 26rpx;
			margin-bottom: 20rpx;
		}

		.picc2 {
			margin-left: 26rpx;
			width: 702rpx;
			height: 220rpx;
		}
	}

	.nav5 {
		.swiper-item {
			height: 100%;
			background: #FFFFFF;
		}
	}
	.nav5Items {
		padding: 8rpx 38rpx 20rpx 32rpx;
		background: #FFFFFF;
		min-height:100rpx;
	
		.item {
			width: 100%;
			height: 268rpx;
			display: flex;
			align-items: center;
			box-sizing: border-box;
			border-bottom: 2rpx solid #F7F8FA;
	
			.pic {
				width: 212rpx;
				height: 212rpx;
				flex-shrink: 0;
			}
	
			.right {
				flex:1;
				padding-left: 20rpx;
				height: 212rpx;
				display: flex;
				flex-direction: column;
				justify-content: space-between;
	
				.up {
					.txt1 {
						font-size: 32rpx;
						font-weight: 500;
						color: #BD9E81;
						display: -webkit-box;
						-webkit-box-orient: vertical;
						-webkit-line-clamp: 2;
						overflow: hidden;
					}
	
					.txt2 {
						margin-top: 10rpx;
						font-size: 24rpx;
						font-weight: 400;
						color: #707070;
					}
				}
	
				.down {
					display: flex;
					align-items: center;
					justify-content: space-between;
	
					.d-t1 {
						font-size: 32rpx;
						font-weight: bold;
						line-height: 44rpx;
						color: #FA8677;
					}
	
					.d-pic {
						width: 40rpx;
						height: 40rpx;
					}
				}
			}
		}
	}
	.tabmenu_sticky{
		position: sticky;
		top:0;
		left:0;
		right:0;
		.tabmenu{
			position: relative;
			width: 100%;
			height: 90rpx;
			padding:0 32rpx;
			background-color: #F7F8FA;
			display: flex;
			align-items: center;
			.tabitem{
				flex:1;
				height: 90rpx;
				display: flex;
				align-items: center;
				justify-content: center;
				font-size: 28rpx;
				color: #707070;
				position: relative;
				&.active{
					color:#BD9E81;
					&::before{
						content:"";
						width: 64rpx;
						height: 4rpx;
						background-color: #BD9E81;
						position: absolute;
						bottom:0;
						left:50%;
						right:0;
						transform: translateX(-50%);
						z-index: 20;
					}
				}
			}
		}
	}
</style>

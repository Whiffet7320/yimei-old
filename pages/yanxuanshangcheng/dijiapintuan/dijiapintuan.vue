<template>
	<view class="index">
		<u-toast ref="uToast" />
		<view class="nav1">
			<u-swiper height='602' :list="bannerList" border-radius="0"></u-swiper>
		</view>
		<view class="nav3">
			<view class="pic2">
				<image src="/static/image/lujin1409.png" class="picc" mode=""></image>
				<view class="txt">{{obj.storeInfo.people||0}}人团</view>
			</view>
			<image src="/static/image/zu1890.png" class="pic" mode=""></image>
			<view class="left">
				<view class="txt1">距结束还剩</view>
				<view class="time1">
					<view class="t1">{{d}}</view>
					<view class="t2">天</view>
					<view class="t1">{{h}}</view>
					<view class="t3">:</view>
					<view class="t1">{{m}}</view>
					<view class="t3">:</view>
					<view class="t1">{{s}}</view>
				</view>
			</view>
			<view class="right">
				<view class="txt1">¥{{obj.storeInfo.price||0.00}}</view>
				<view class="txt2">¥ {{obj.storeInfo.product_price||0.00}}</view>
			</view>
		</view>
		<view class="nav2">
			<view class="left">
				<view class="txt1">{{obj.storeInfo.title||""}}</view>
				<view class="txt2">{{obj.storeInfo.num||""}}{{obj.storeInfo.unit_name||""}}</view>
			</view>
		</view>
		<view class='assemble'>
			<view class='item' v-for='(item,index) in pink' :key='index' v-if="index < AllIndex">
				<view class='pictxt'>
					<view class='pictrue'>
						<image :src='item.avatar'></image>
					</view>
					<view class='text u-line-1'>{{item.nickname}}</view>
				</view>
				<view class='right'>
					<view>
						<view class='lack'>还差<text class='font-num'>{{item.count}}</text>人成团</view>
						<view class='time'>
							<count-down :is-day="false" :tip-text="' '" :day-text="' '" :hour-text="':'" :minute-text="':'" :second-text="' '" :datatime="item.stop_time"></count-down>
						</view>
					</view>
					<view class='spellBnt' @click="goPink(item.id)">去拼单</view>
				</view>
			</view>
			<template v-if="pink.length">
				<view class='more' @tap='showAll' v-if="pink.length > AllIndex">查看更多</view>
				<view class='more' @tap='hideAll' v-else-if="pink.length === AllIndex && pink.length !== AllIndexDefault">收起</view>
			</template>
		</view>
		
		<!-- <view class="tabmenu_sticky">
			<view class="tabmenu">
				<view class="tabitem" :class="{'active':current==index}" v-for="(item,index) in list" :key="index" @click="tabsChange(index)">{{item.name}}</view>
			</view>
		</view>
		<view class="tabcont">
			<view v-if="swiperCurrent == 0">
				<view class="i1" v-if="obj.doctor_info.doctor_product_img">
					<image :src="obj.doctor_info.doctor_product_img" mode="widthFix"></image>
				</view>
			</view>
			<view class="i3" v-if="swiperCurrent == 1">
				<comment-list :Obj="pingjiaObj" :list="pinglunList" @getPinglunData="getPinglunData" @toSeeImg="toSeeImg"></comment-list>
				<u-loadmore :status="status" font-size="22" />
			</view>
		</view> -->
		<view class="nav5">
			<view class="title">
				<view class="shu"></view>
				<view class="t-txt">产品详情</view>
			</view>
			<view class="content">
				<u-parse :html="obj.storeInfo.description"></u-parse>
			</view>
		</view>
		<u-gap height="140"></u-gap>
		<product-sku :attr='attribute' :limitNum='1' @myevent="onMyEvent" @ChangeAttr="ChangeAttr" @ChangeCartNum="ChangeCartNum" @iptCartNum="iptCartNum" @attrVal="attrVal" ></product-sku>
		<view class="footer" v-if="attribute.productSelect.product_stock>0&&attribute.productSelect.quota>0">
			<view @click="kaituan" class="btn">我要开团</view>
		</view>
		<view class="footer" v-if="attribute.productSelect.quota <= 0 || attribute.productSelect.product_stock <= 0">
			<view class="btn gray">已售罄</view>
		</view>
	</view>
</template>

<script>
	import {mapState} from "vuex";
	import countDown from '@/components/countDown/index.vue';
	import ProductSku from '@/components/productSku';
	import commentList from "@/components/commentList";
	export default {
		components:{
			countDown,
			ProductSku,
			commentList
		},
		data() {
			return {
				pingjiaObj:{},
				pinglunList: [],
				id: '',
				obj: {
					doctor_info:{
						doctor_product_img:''
					},
					storeInfo:{}
				},
				d: '0',
				h: '00',
				m: '00',
				s: '00',
				isOnShow: true,
				navTitle: '',
				bannerList: [],
				imgArr: [],
				//
				swiperCurrentIndex: 0,
				height: 0,
				list: [{
					name: '医生描述'
				}, {
					name: '买家评价'
				}],
				// 因为内部的滑动机制限制，请将tabs组件和swiper组件的current用不同变量赋值
				current: 0, // tabs组件的current值，表示当前活动的tab选项
				swiperCurrent: 0, // swiper组件的current值，表示当前那个swiper-item是活动的
				// 加载
				status: 'loadmore',
				page:1,
				storeInfo:{},
				attribute: {
					cartAttr: false,
					productAttr: [],
					productSelect: {}
				},
				productValue: [],
				isOpen: false,
				attr: '请选择',
				attrValue: '',
				pink:[],
				AllIndex: 2,
				AllIndexDefault: 0,
				attrTxt: '',
				cart_num: '',
				skuArr: [],
				selectSku: {}
			}
		},
		onLoad(options) {
			if(options.id){
				this.id = options.id;
				this.tabsChange(0);
			}
		},
		onShow() {
			if (!this.isOnShow) {
				return;
			}
		},
		// mounted() {
		// 	setTimeout(() => {
		// 		this.getCurrentSwiperHeight('.nav5Items')
		// 	}, 900)
		// },
		onReachBottom() {
			this.getPinglunData();
		},
		methods: {
			showAll() {
				this.AllIndexDefault = this.AllIndex;
				this.AllIndex = this.pink.length;
			},
			hideAll() {
				this.AllIndex = this.AllIndexDefault;
			},
			async getData2() {
				this.bannerList = [];
				const res = await this.$api.combinationDetail(this.id);
				if(res.status==200){
					uni.setNavigationBarTitle({
						title:res.data.storeInfo.title.substring(0, 16)
					})
					this.storeInfo = res.data.storeInfo;
					this.obj = res.data;
					this.storeInfo.images.forEach(ele => {
						this.bannerList.push({
							image: ele
						})
					})
					this.attribute.productAttr = res.data.productAttr;
					this.productValue = res.data.productValue;
					this.pink = res.data.pink;
					this.mygetdate(this.obj.storeInfo.stop_time);
					this.DefaultSelect();
				}
			},
			async getPinglunData() {
				this.status = 'loading';
				setTimeout(async () => {
					const res = await this.$api.replyList({
						page:this.page,
						limit:10
					}, this.id)
					if(res.status==200){
						this.pingjiaObj = res.data.comment;
						if(res.data.list.length==0){
							this.status = 'nomore';
							return false;
						}
						if(this.page!=1){
							this.pinglunList = this.pinglunList.concat(res.data.list);
						}else{
							if(res.data.list.length<10){
								this.status = 'nomore';
							}
							this.pinglunList = res.data.list;
						}
						this.page ++;
					}
				}, 200)
			},
			reloadData(){
				this.pinglunList = [];
				this.page = 1;
				this.getPinglunData();
			},
			/**
			 * 购物车手动填写
			 * 
			 */
			iptCartNum(e) {
				this.$set(this.attribute.productSelect, 'cart_num', e);
				this.$set(this, "cart_num", e);
			},
			selecAttr() {
				this.attribute.cartAttr = true;
			},
			onMyEvent() {
				this.$set(this.attribute, 'cartAttr', false);
				this.$set(this, 'isOpen', false);
			},
			/**
			 * 购物车数量加和数量减
			 * 
			 */
			ChangeCartNum(changeValue) {
				//changeValue:是否 加|减
				//获取当前变动属性
				let productSelect = this.productValue[this.attrValue];
				if (this.cart_num) {
					productSelect.cart_num = this.cart_num;
					this.attribute.productSelect.cart_num = this.cart_num;
				}
				//如果没有属性,赋值给商品默认库存
				if (productSelect === undefined && !this.attribute.productAttr.length)
					productSelect = this.attribute.productSelect;
				//无属性值即库存为0；不存在加减；
				if (productSelect === undefined) return;
				let stock = productSelect.stock || 0;
				let quotaShow = productSelect.quota_show || 0;
				let quota = productSelect.quota || 0;
				let productStock = productSelect.product_stock || 0;
				let num = this.attribute.productSelect;
				let nums = this.storeInfo.num || 0;
				//设置默认数据
				if (productSelect.cart_num == undefined) productSelect.cart_num = 1;
				if (changeValue) {
					num.cart_num++;
					let arrMin = [];
					arrMin.push(nums);
					arrMin.push(quota);
					arrMin.push(productStock);
					let minN = Math.min.apply(null, arrMin);
					if (num.cart_num >= minN) {
						this.$set(this.attribute.productSelect, "cart_num", minN ? minN : 1);
						this.$set(this, "cart_num", minN ? minN : 1);
					}
					this.$set(this, "cart_num", num.cart_num);
					this.$set(this.attribute.productSelect, "cart_num", num.cart_num);
				} else {
					num.cart_num--;
					if (num.cart_num < 1) {
						this.$set(this.attribute.productSelect, "cart_num", 1);
						this.$set(this, "cart_num", 1);
					}
					this.$set(this, "cart_num", num.cart_num);
					this.$set(this.attribute.productSelect, "cart_num", num.cart_num);
				}
			},
			attrVal(val) {
				this.attribute.productAttr[val.indexw].index = this.attribute.productAttr[val.indexw].attr_values[val.indexn];
			},
			/**
			 * 属性变动赋值
			 * 
			 */
			ChangeAttr(res) {
				this.$set(this, 'cart_num', 1);
				let productSelect = this.productValue[res];
				this.$set(this, "selectSku", productSelect);
				if (productSelect) {
					this.$set(this.attribute.productSelect, "image", productSelect.image);
					this.$set(this.attribute.productSelect, "price", productSelect.price);
					this.$set(this.attribute.productSelect, "ot_price", productSelect.ot_price);
					this.$set(this.attribute.productSelect, "unit_name",this.storeInfo.unit_name);
					this.$set(this.attribute.productSelect, "stock", productSelect.stock);
					this.$set(this.attribute.productSelect, "unique", productSelect.unique);
					this.$set(this.attribute.productSelect, "cart_num", 1);
					this.$set(this.attribute.productSelect, "quota", productSelect.quota);
					this.$set(this.attribute.productSelect, "quota_show", productSelect.quota_show);
					this.$set(this, "attrValue", res);
					this.attrTxt = "已选择";
				} else {
					this.$set(this.attribute.productSelect, "image", this.storeInfo.image);
					this.$set(this.attribute.productSelect, "price", this.storeInfo.price);
					this.$set(this.attribute.productSelect, "ot_price", productSelect.ot_price);
					this.$set(this.attribute.productSelect, "unit_name",this.storeInfo.unit_name);
					this.$set(this.attribute.productSelect, "stock", 0);
					this.$set(this.attribute.productSelect, "unique", "");
					this.$set(this.attribute.productSelect, "cart_num", 0);
					this.$set(this.attribute.productSelect, "quota", 0);
					this.$set(this.attribute.productSelect, "quota_show", 0);
					this.$set(this, "attrValue", "");
					this.attrTxt = "已选择";
				}
			},
			//默认选中
			DefaultSelect() {
				let productAttr = this.attribute.productAttr;
				let value = [];
				for (var key in this.productValue) {
					if (this.productValue[key].quota > 0) {
						value = this.attribute.productAttr.length ? key.split(",") : [];
					}
				}
				for (let i = 0; i < productAttr.length; i++) {
					this.$set(productAttr[i], "index", value[i]);
				}
				let productSelect = this.productValue[value.join(",")];
				if (productSelect && productAttr.length) {
					this.$set(this.attribute.productSelect,"store_name",this.storeInfo.title);
					this.$set(this.attribute.productSelect, "image", productSelect.image);
					this.$set(this.attribute.productSelect, "price", productSelect.price);
					this.$set(this.attribute.productSelect, "ot_price", productSelect.ot_price);
					this.$set(this.attribute.productSelect, "unit_name",this.storeInfo.unit_name);
					this.$set(this.attribute.productSelect, "stock", productSelect.stock);
					this.$set(this.attribute.productSelect, "unique", productSelect.unique);
					this.$set(this.attribute.productSelect, "quota", productSelect.quota);
					this.$set(this.attribute.productSelect, "quota_show", productSelect.quota_show);
					this.$set(this.attribute.productSelect, "product_stock", productSelect.product_stock);
					this.$set(this.attribute.productSelect, "cart_num", 1);
					this.attrValue = value.join(",");
				} else if (!productSelect && productAttr.length) {
					this.$set(this.attribute.productSelect,"store_name",this.storeInfo.title);
					this.$set(this.attribute.productSelect, "image", this.storeInfo.image);
					this.$set(this.attribute.productSelect, "price", this.storeInfo.price);
					this.$set(this.attribute.productSelect, "ot_price", this.storeInfo.product_price);
					this.$set(this.attribute.productSelect, "unit_name",this.storeInfo.unit_name);
					this.$set(this.attribute.productSelect, "quota", 0);
					this.$set(this.attribute.productSelect, "quota_show", 0);
					this.$set(this.attribute.productSelect, "product_stock", 0);
					this.$set(this.attribute.productSelect, "stock", 0);
					this.$set(this.attribute.productSelect, "unique", "");
					this.$set(this.attribute.productSelect, "cart_num", 0);
					this.$set(this, "attrValue", "");
					this.$set(this, "attrTxt", "请选择");
				} else if (!productSelect && !productAttr.length){
					this.$set(this.attribute.productSelect,"store_name",this.storeInfo.title);
					this.$set(this.attribute.productSelect, "image", this.storeInfo.image);
					this.$set(this.attribute.productSelect, "price", this.storeInfo.price);
					this.$set(this.attribute.productSelect, "ot_price", this.storeInfo.product_price);
					this.$set(this.attribute.productSelect, "unit_name",this.storeInfo.unit_name);
					this.$set(this.attribute.productSelect, "stock", this.storeInfo.stock);
					this.$set(this.attribute.productSelect, "quota", 0);
					this.$set(this.attribute.productSelect, "product_stock", 0);
					this.$set(this.attribute.productSelect,"unique",this.storeInfo.unique || "");
					this.$set(this.attribute.productSelect, "cart_num", 1);
					this.$set(this, "attrValue", "");
					this.$set(this, "attrTxt", "请选择");
				}
			},
			async kaituan() {
				let productSelect = this.productValue[this.attrValue];
				//打开属性
				if (this.isOpen){
					this.attribute.cartAttr = true;
				}else{
					this.attribute.cartAttr = !this.attribute.cartAttr;
				}
				//只有关闭属性弹窗时进行加入购物车
				if (this.attribute.cartAttr === true && this.isOpen == false) return this.isOpen = true;
				if (this.attribute.productAttr.length && productSelect === undefined && this.isOpen == true){
					this.$u.toast('请选择属性');
					return false;
				}
				let data = {
					cartNum:this.attribute.productSelect.cart_num,
					combinationId:this.obj.storeInfo.id,
					new:1,
					productId:this.obj.storeInfo.product_id,
					uniqueId:productSelect !== undefined ? productSelect.unique : ''
				}
				const res = await this.$api.cartAdd(data);
				if(res.status==200){
					uni.navigateTo({
						url:`/pages/users/order/tijiaodingdan?cartId=${res.data.cartId}&new=1`
					})
				}else{
					this.$u.toast(res.msg);
				}
			},
			mygetdate(startSellTime) {
				var date = new Date();
				var now = date.getTime();
				var endTime = new Date(startSellTime * 1000); // 结束时间
				var end = endTime.getTime();
				var lefttime = end - now;
				var d, h, m, s;
				if (lefttime > 0) {

					h = Math.floor(Math.floor(lefttime / 1000 / 60 / 60 / 24) * 24 + (lefttime / 1000 / 60 / 60 % 24))
					d = Math.floor(h / 24);
					h = d % 24;
					m = Math.floor(lefttime / 1000 / 60 % 60)
					s = Math.floor(lefttime / 1000 % 60)
					if (s < 10) {
						s = '0' + s
					}
					if (m < 10) {
						m = '0' + m
					}
					if (h < 10) {
						h = '0' + h
					}
				} else {
					this.d = '0';
					this.h = '00';
					this.m = '00';
					this.s = '00';
					// 清除定时器
					clearTimeout(timer)
				}
				// console.log(d + '天' +h + '时' + m + '分' + s + '秒后开始')
				this.d = d;
				this.h = h;
				this.m = m;
				this.s = s;
				var timer = setTimeout(() => {
					this.mygetdate(startSellTime)
				}, 1000);
			},
			toSeeImg(val) {
				let i = val.index, imgArr = val.imgArr;
				this.isOnShow = false;
				uni.previewImage({
					current: i,
					urls: imgArr,
					success: (res) => {
						console.log(res)
					}
				})
			},
			toShouyintai() {
				uni.navigateTo({
					url: '/pages/index/search/querendingdan'
				})
			},
			// tabs通知swiper切换
			tabsChange(index) {
				this.swiperCurrent = index;
				this.swiperCurrentIndex = index;
				this.current = index;
				if(index==0){
					this.getData2();
				}else if(index==1){
					this.reloadData();
				}
			},
			animationfinish(e) {
				let current = e.detail.current;
				this.$refs.uTabs.setFinishCurrent(current);
				this.swiperCurrent = current;
				this.current = current;
			},
			getCurrentSwiperHeight(element) {
				let query = uni.createSelectorQuery().in(this);
				query.selectAll(element).boundingClientRect();
				query.exec((res) => {
					this.height = res[0][this.swiperCurrentIndex].height;
				})
			},
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
		height: 100rpx !important;
	}

	.index {
		position: relative;
	}

	.nav3 {
		position: relative;
		height: 150rpx;

		.pic {
			position: absolute;
			top: 0;
			left: 0;
			// margin-top: -16rpx;
			height: 150rpx;
			width: 100%;
		}

		.pic2 {
			z-index: 9;
			width: 132rpx;
			height: 80rpx;
			position: absolute;
			right: 24rpx;
			bottom: 114rpx;

			.picc {
				position: absolute;
				top: 0;
				left: 0;
				width: 132rpx;
				height: 80rpx;
			}

			.txt {
				position: absolute;
				top: 0;
				left: 0;
				width: 142rpx;
				height: 80rpx;
				font-size: 24rpx;
				font-weight: bold;
				line-height: 80rpx;
				text-align: center;
				color: #BD9E81;
			}
		}

		.left {
			position: absolute;
			top: 30rpx;
			left: 0;
			width: calc(750rpx - 358rpx);

			.txt1 {
				font-size: 28rpx;
				font-weight: 500;
				color: #707070;
				text-align: center;
			}

			.time1 {
				margin-top: 8rpx;
				justify-content: center;
				display: flex;
				align-items: center;

				.t1 {
					width: 44rpx;
					height: 42rpx;
					background: #BD9E81;
					border-radius: 10rpx;
					font-size: 22rpx;
					font-weight: bold;
					line-height: 42rpx;
					text-align: center;
					color: #FFFFFF;
				}

				.t2 {
					margin: 0 10rpx;
					font-size: 24rpx;
					font-weight: 400;
					color: #707070;
				}

				.t3 {
					margin: 0 12rpx;
					font-size: 24rpx;
					font-weight: 400;
					color: #707070;
					line-height: 42rpx;
				}
			}

			// .txt2 {
			// 	text-align: center;
			// 	font-size: 30rpx;
			// 	font-family: PingFang SC;
			// 	font-weight: 800;
			// 	line-height: 56rpx;
			// 	color: #707070;

			// 	.big {
			// 		font-size: 40rpx;
			// 	}
			// }
		}

		.right {
			position: absolute;
			top: 30rpx;
			right: 0;
			width: 360rpx;

			.txt1 {
				font-size: 40rpx;
				font-weight: 800;
				line-height: 56rpx;
				color: #FFFFFF;
				text-align: center;
			}

			.txt2 {
				text-decoration: line-through;
				text-align: center;
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 400;
				line-height: 34rpx;
				color: #ffffff;

				.big {
					font-size: 40rpx;
				}
			}
		}
	}

	.nav2 {
		margin: 20rpx 0 20rpx 0;
		display: flex;
		justify-content: space-between;
		background: #FFFFFF;
		padding: 28rpx 40rpx;

		.left {
			display: flex;
			flex-direction: column;
			justify-content: space-between;

			.txt1 {
				font-size: 32rpx;
				font-weight: 500;
				color: #BD9E81;
			}

			.txt2 {
				margin-top: 8rpx;
				font-size: 24rpx;
				font-weight: 400;
				color: #707070;
			}
		}

		.right {
			display: flex;
			align-items: center;
			flex-direction: column;
			justify-content: space-between;

			.r-pic {
				width: 42rpx;
				height: 42rpx;
			}

			.txt1 {
				margin-top: 8rpx;
				font-size: 24rpx;
				font-weight: 400;
				color: #707070;
			}
		}
	}

	.nav4 {
		margin-bottom: 140rpx;

		// margin-bottom: 240rpx;
		/deep/ .u-tabs-scroll-box {
			padding: 0 60rpx;

			.u-scroll-bar {
				margin-left: 60rpx;
			}
		}

		.swiper-item {
			// background: #FFFFFF;
		}

		.nav5Items {
			// padding: 8rpx 24rpx 20rpx 24rpx;
			// background: #FFFFFF;
			display: flex;
			flex-wrap: wrap;
			justify-content: space-between;
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
			height: 80rpx;
			background-color: #FFFFFF;
			display: flex;
			.tabitem{
				flex:1;
				height: 80rpx;
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
						height: 2rpx;
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
	.tabcont{
		width: 100%;
		.i1{
			width: 100%;
			text-align: center;
		}
		.i3{
			background-color: #FFFFFF;
			margin-top: 20rpx;
		}
	}

	.footer1 {
		position: fixed;
		bottom: 140rpx;
		width: 100%;
		height: 88rpx;
		background: #F2ECE6;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;

		.kefu {
			position: absolute;
			bottom: 14rpx;
			right: 8rpx;
			width: 172rpx;
			height: 172rpx;
		}

		.txt1 {
			font-size: 24rpx;
			font-family: PingFang SC;
			line-height: 34rpx;
			color: #BD9E81;
		}

		.txt2 {
			font-size: 24rpx;
			font-family: PingFang SC;
			line-height: 34rpx;
			color: #BD9E81;
		}
	}

	.footer2 {
		position: fixed;
		bottom: 0;
		width: 100%;
		height: 140rpx;
		background: #FFFFFF;
		box-shadow: 0rpx 8rpx 28rpx rgba(166, 179, 194, 0.3);
		display: flex;
		padding: 0 50rpx;

		.item {
			margin-top: 12rpx;
			height: 80rpx;
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: space-between;

			.pic {
				width: 50rpx;
				height: 50rpx;
			}

			.txt {
				font-size: 24rpx;
				font-weight: 400;
				color: #707070;
			}

			.pic2 {
				margin-top: 4rpx;
				width: 40rpx;
				height: 40rpx;
			}
		}

		.item2 {
			margin-left: 76rpx;
		}

		.btn {
			border-radius: 40rpx;
			margin-left: 70rpx;
			margin-top: 12rpx;
			width: 406rpx;
			height: 80rpx;
			font-size: 28rpx;
			font-weight: bold;
			line-height: 80rpx;
			text-align: center;
			color: #FFFFFF;
			background: #BD9E81;
			&.gray{
				background: #bbb;
			}
		}
	}

	.footer {
		position: fixed;
		left:0;
		right:0;
		bottom: 0;
		z-index: 200;
		width: 100%;
		height: 140rpx;
		background: #FFFFFF;
		box-shadow: 0rpx 8rpx 28rpx rgba(166, 179, 194, 0.3);

		.btn {
			width: 682rpx;
			height: 84rpx;
			background: #BD9E81;
			border-radius: 44rpx;
			margin-top: 10rpx;
			margin-left: 34rpx;
			font-size: 28rpx;
			font-weight: bold;
			line-height: 84rpx;
			color: #FFFFFF;
			text-align: center;
		}
	}
	.assemble {
	  background-color: #fff;
	  margin-bottom: 20rpx;
	  .item {
	    padding-right: 30rpx;
	    margin-left: 30rpx;
	    border-bottom: 1rpx solid #f0f0f0;
	    height: 132rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		.pictxt {
		  width: 295rpx;
		  display: flex;
		  align-items: center;
		  .text {
		    width: 194rpx;
		  }
		  .pictrue {
		    width: 80rpx;
		    height: 80rpx;
			image {
			  width: 100%;
			  height: 100%;
			  border-radius: 50%;
			  background-color: #eee;
			}
		  }
		}
		.right{
			display: flex;
			align-items: center;
			.lack {
			  font-size: 24rpx;
			  color: #333333;
			  .font-num{
			  	color: #fc0000;
			  }
			}
			.time {
			  position: relative;
			  left: -10rpx;
			  font-size: 22rpx;
			  color: #82848f;
			  margin-top: 5rpx;
			}
			.spellBnt {
			  font-size: 24rpx;
			  color: #fff;
			  width: 140rpx;
			  height: 50rpx;
			  border-radius: 50rpx;
			  text-align: center;
			  line-height: 50rpx;
			  background-color: #BD9E81;
			  margin-left: 30rpx;
			}
		}
	  }
	  .more {
	    font-size: 24rpx;
	    color: #282828;
	    text-align: center;
	    height: 90rpx;
	    line-height: 90rpx;
	  }
	}
	
	.nav5 {
		margin-top: 20rpx;
		background: #FFFFFF;
	
		.title {
			display: flex;
			align-items: center;
			padding: 24rpx 0;
	
			.shu {
				margin-left: 24rpx;
				margin-right: 20rpx;
				width: 6rpx;
				height: 28rpx;
				background: #BD9E81;
			}
	
			.t-txt {
				font-size: 28rpx;
				font-weight: bold;
				color: #BD9E81;
			}
		}
	
		.content {
			width: 100%;
			padding: 0 24rpx;
		}
	}
	
</style>

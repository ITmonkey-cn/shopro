<!-- 优惠劵列表  -->
<template>
	<view class="page_box">
		<view class="head_box u-m-b-20">
			<view class="coupon-nav u-flex">
				<view class="nav-item u-flex-col u-flex-1 u-row-center u-col-center" v-for="nav in couponsState" :key="nav.id" @tap="onNav(nav.id)">
					<view class="item-title">{{ nav.title }}</view>
					<text class="nav-line" :class="{ 'line-active': stateCurrent === nav.id }"></text>
				</view>
			</view>
		</view>
		<view class="content_box">
			<view class="coupon-list" v-for="(c, index) in couponList" :key="index" @tap="toCouponDetail(c)">
				<shopro-coupon :state="stateCurrent" :couponData="c"></shopro-coupon>
			</view>
			<!-- 缺省页 -->
			<shopro-empty marginTop="300rpx" v-show="isEmpty" :image="$IMG_URL + '/imgs/empty/empty_goods.png'" tipText="暂无此类优惠券"></shopro-empty>
		</view>
		<!-- 登录提示 -->
		<shopro-auth-modal></shopro-auth-modal>
	</view>
</template>

<script>
export default {
	components: {},
	data() {
		return {
			stateCurrent: 0,
			isEmpty: false,
			couponsState: [
				{
					id: 0,
					title: '领券中心'
				},
				{
					id: 1,
					title: '可使用'
				},
				{
					id: 2,
					title: '已使用'
				},
				{
					id: 3,
					title: '已失效'
				}
			],
			couponList: []
		};
	},
	computed: {},
	onLoad() {
		this.getCouponList();
	},
	methods: {
		onNav(id) {
			if (this.stateCurrent !== id) {
				this.stateCurrent = id;
				this.couponList = [];
				this.getCouponList();
			}
		},
		jump(path, parmas) {
			this.$Router.push({
				path: path,
				query: parmas
			});
		},
		getCouponList() {
			let that = this;
			that.$http('coupons.list', {
				type: that.stateCurrent
			}).then(res => {
				if (res.code === 1) {
					that.couponList = res.data;
					that.isEmpty = !that.couponList.length;
				}
			});
		},

		//跳转优惠券详情
		toCouponDetail(data) {
			if (data.user_coupons_id) {
				this.jump('/pages/app/coupon/detail', { id: data.id, userCouponId: data.user_coupons_id });
			} else {
				this.jump('/pages/app/coupon/detail', { id: data.id });
			}
		}
	}
};
</script>

<style lang="scss">
.coupon-nav {
	background: #fff;
	height: 100rpx;

	.nav-item {
		flex: 1;
		height: 100%;
		.item-title {
			font-size: 30rpx;
			font-weight: 400;
			color: rgba(51, 51, 51, 1);
			line-height: 94rpx;
		}

		.nav-line {
			width: 120rpx;
			height: 4rpx;
			background: #fff;
		}

		.line-active {
			background: rgba(230, 184, 115, 1);
		}
	}
}
.coupon-list {
	margin: 30rpx 20rpx;
}
</style>

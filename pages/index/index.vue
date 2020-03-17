<template>
	<view class="content">
		<view class="banner">
			<swiper class="swiper" @change="changeCurrent" :current="indexNum" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval"
			 :duration="duration">
				<swiper-item v-for="(item, index) in swiperList" :key="index">
					<image class="swiper-image" :src="item.url" mode="aspectFit"></image>
				</swiper-item>
			</swiper>
			<view class="swiper-dot-box">
				<view v-for="(item, index) in swiperList" :key="index" class="dot" :class="{ active: index === indexNum }"></view>
			</view>
		</view>
		<view class="inner">
			<view class="title-wrap">
				<view class="title-left">
					<text class="iconfont service icon"></text>
					<text class="text">服务</text>
				</view>
				<view class="title-right">
					<navigator url="">
						<text class="text">服务指南</text>
						<text class="iconfont arrow-right icon"></text>
					</navigator>
				</view>
			</view>
			<view class="sevice-box">
				<navigator url="../oneline-apply/oneline-apply" class="nav-box">
					<view class="icon-box">
						<text class="iconfont mediation icon"></text>
					</view>
					<view class="text">在线调解</view>
				</navigator>
				<view class="nav-box" style="background-color: #006dff;">
					<view class="icon-box">
						<text class="iconfont consulting icon"></text>
					</view>
					<view class="text">留言咨询</view>
				</view>
				<navigator url="../team/team" class="nav-box" style="background-color: #009cff;">
					<view class="icon-box">
						<text class="iconfont team icon"></text>
					</view>
					<view class="text">调解队伍</view>
				</navigator>
			</view>
			<view class="title-wrap">
				<view class="title-left">
					<text class="iconfont project icon"></text>
					<text class="text">专题</text>
				</view>
			</view>
			<view class="subject-wrap">
				<navigator url="../tvDetail/tvDetail" class="subject-box">
					<image class="card-image" src="../../static/images/project.png"></image>
					<view class="text-wrap">
						<view class="card-title">帮忙有一套</view>
						<view class="card-text">
							《帮忙有一套》电视节目预告、回访及直播。
						</view>
					</view>
				</navigator>
				<navigator class="subject-box">
					<image class="card-image" src="../../static/images/project.png"></image>
					<view class="text-wrap">
						<view class="card-title">金融调解</view>
						<view class="card-text">
							金融调解专题模块，包含金融资讯、人员推荐、金融在线调解。
						</view>
					</view>
				</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				indicatorDots: false,
				autoplay: true,
				interval: 3000,
				duration: 500,
				indexNum: 0,
				swiperList: [{
						name: '',
						url: '../../static/images/home_banner.png'
					},
					{
						name: '',
						url: '../../static/images/home_banner.png'
					},
				]
			}
		},
		onLoad() {

		},
		methods: {
      changeCurrent(e) {
				this.indexNum = e.detail.current;
			}
		}
	}
</script>

<style lang="scss">
	.swiper-image {
		width: 100%;
		height: 100%;
	}

	.swiper-dot-box {
		display: flex;
		justify-content: flex-start;
		padding: 0 32upx;

		.dot {
			margin: 0 8upx;
			width: 40upx;
			height: 8upx;
			background-color: #ecedf0;

			&.active {
				background-color: #9ea5ba;
			}
		}
	}

	.inner {
		padding: 40upx;
	}

	.title-wrap {
		display: flex;
		justify-content: space-between;
		padding: 20upx 0;
	}

	.title-left {
		.icon {
			margin-right: 16upx;
			padding: 5upx;
			color: #fff;
			font-size: 34upx;
			background-color: #9ea5ba;
		}

		.text {
			color: #9ea5ba;
			font-size: 28upx;
		}
	}

	.title-right {
		.text {
			font-size: 28upx;
			color: #9ea5ba;
		}

		.icon {
			font-size: 28upx;
			color: #9ea5ba;
		}
	}

	.sevice-box {
		padding: 10upx 0 50upx;
		display: flex;
		justify-content: space-between;
	}

	.nav-box {
		width: calc((100% - 40upx)/3);
		padding: 40upx 0 32upx;
		text-align: center;
		background-color: #0032e7;
    box-shadow: 0 0 8upx rgba(#0032e7, .4);
		.icon-box {
			display: flex;
			justify-content: center;
			align-items: center;
			width: 88upx;
			height: 88upx;
			margin: 0 auto 8upx;
			background-color: rgba(#000, .2);
			border-radius: 50%;

			.icon {
				font-size: 48upx;
				color: #fff;
			}
		}

		.text {
			color: #fff;
			font-size: 30upx;
		}
	}

	.subject-wrap {
		.subject-box {
			margin-bottom: 20upx;
			padding: 30upx;
			display: flex;
			background-color: #f5f6fa;

			.card-image {
				width: 160upx;
				height: 160upx;
			}

			.text-wrap {
				flex: 1;
				display: flex;
				flex-direction: column;
				padding-left: 24upx;
			}
			
			.card-title {
				flex: 1;
				font-size: 34upx;
				color: $dark-text;
			}
			
			.card-text {
				color: $light-text;
				font-size: 28upx;
			}
		}
	}
</style>

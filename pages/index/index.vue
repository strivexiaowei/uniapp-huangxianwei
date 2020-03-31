<template>
	<view class="content">
		<view class="banner">
			<swiper class="swiper" @change="changeCurrent" :current="indexNum" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item, index) in swiperList" :key="index"><image class="swiper-image" :src="item.url" mode="aspectFit"></image></swiper-item>
			</swiper>
			<view class="swiper-dot-box"><view v-for="(item, index) in swiperList" :key="index" class="dot" :class="{ active: index === indexNum }"></view></view>
		</view>
		<view class="inner">
			<view class="title-wrap">
				<view class="title-left">
					<text class="iconfont service icon"></text>
					<text class="text">服务</text>
				</view>
			</view>
			<view class="sevice-box">
				<block v-for="(item, index) in navList" :key="index">
					<navigator :url="item.url" class="nav-box" :style="{ 'background-color': item.bgColor }">
						<view class="icon-box"><text class="iconfont icon" :class="[item.icon]"></text></view>
						<view class="text">{{ item.name }}</view>
					</navigator>
				</block>
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
						<view class="card-text-wrap">
							<view class="card-text">
								《帮忙有一套》电视节目预告、回访及直播。
							</view>
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
			swiperList: [
				{
					name: '',
					url: '../../static/images/home_banner.png'
				},
				{
					name: '',
					url: '../../static/images/home_banner.png'
				}
			],
			navList: [
				{
					name: '在线调解',
					url: '../oneline-apply/oneline-apply',
					bgColor: '#0032e7',
					icon: 'mediation'
				},
				{
					name: '调解队伍',
					url: '../new-team/new-team',
					bgColor: '#009cff',
					icon: 'team'
				}
			]
		};
	},
	onLoad() {},
	methods: {
		changeCurrent(e) {
			this.indexNum = e.detail.current;
		}
	}
};
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
	display: flex;
	align-items: center;
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
	width: calc((100% - 20upx) / 2);
	padding: 40upx 0 32upx;
	text-align: center;
	box-shadow: 0 0 8upx rgba(#0032e7, 0.4);
	.icon-box {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 88upx;
		height: 88upx;
		margin: 0 auto 8upx;
		background-color: rgba(#000, 0.2);
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
			overflow: hidden;
		}

		.card-title {
			font-size: 34upx;
			color: $dark-text;
			white-space: nowrap;
			text-overflow: ellipsis;
			overflow: hidden;
			word-break: break-all;
		}
    .card-text-wrap {
			flex: 1;
			display: flex;
			align-items: center;
		}
		.card-text {
			text-overflow: -o-ellipsis-lastline;
			overflow: hidden;
			text-overflow: ellipsis;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			line-clamp: 2;
			-webkit-box-orient: vertical;
			color: $light-text;
			font-size: 28upx;
		}
	}
}
</style>

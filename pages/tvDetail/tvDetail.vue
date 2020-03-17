<template>
	<view class="cantent">
		<video id="myVideo" :src="playUrl" :poster="'/api/service/rest/tk.File/' + id + '/view'" controls></video>
		<view class="inner">
			<view class="title">节目详情</view>
			<view class="description-list">
				<view class="description-item">
					<view class="desc-label">节目名称</view>
					<view class="desc-value">{{ name }}</view>
				</view>
				<view class="description-item">
					<view class="desc-label">直播时间</view>
					<view class="desc-value">{{ playTime }}</view>
				</view>
				<view class="description-item">
					<view class="desc-label">节目时长</view>
					<view class="desc-value">{{ duration }}</view>
				</view>
				<view class="description-item">
					<view class="desc-label">播放次数</view>
					<view class="desc-value">{{ playCount }}</view>
				</view>
				<view class="description-item">
					<view class="desc-label">节目简介</view>
					<view class="desc-value">{{ introduction }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        id: '0ca16c1c979a4895b1ae2a146773182e',
				playUrl: '',
				name: '',
				playTime: '',
				duration: '',
				introduction: '',
				lowSource: '',
				playCount: '',
				fileID: ''
			};
		},
		onLoad() {
			this.getTvDetail(this.id);
		},
		methods: {
			getTvDetail(id) {
				uni.request({
					url: 'http://shanxi.tunnel.homolo.org/api/helpOutService/getRepeatProgrammeInfo', //仅为示例，并非真实接口地址。
					method: 'GET',
					data: {
						id
					},
					success: (res) => {
						console.log(res);
						const { fileID, playCount, playUrl, name, playTime, duration, introduction, lowSource } = res.data.result;
						this.playUrl = playUrl;
						this.name = name;
						this.playTime = playTime;
						this.duration = duration;
						this.introduction = introduction;
						this.lowSource = lowSource;
						this.playCount = playCount;
						this.fileID = fileID;
					}
				});
			}
		}
	}
</script>

<style lang="scss">
	.content {
		background-color: #f5f5f5;
	}

	#myVideo {
		width: 100%;
	}

	.inner {
		padding: 40upx 40upx 120upx;
		background-color: #fff;

		.title {
			padding-bottom: 30upx;
			font-size: 36upx;
			color: $dark-text;
		}

		.description-item {
			display: flex;
			padding: 10upx 0;

			.desc-label {
				width: 120upx;
				margin-right: 30upx;
				font-size: 30upx;
				color: $dark-text;
			}

			.desc-value {
				flex: 1;
				font-size: 30upx;
				color: $light-text;
			}

		}
	}
</style>

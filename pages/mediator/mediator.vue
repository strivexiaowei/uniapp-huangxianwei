<template>
	<view class="content">
		<view class="mediator-info-box">
			<image class="head-image" :src="getImgUrl(fileId)" mode="aspectFill"></image>
			<view class="name">{{ name }}</view>
			<view class="label-info-wrap">
				<view class="label">调解区域</view>
				<view class="tag">{{ DQBM }}</view>
			</view>
			<view class="label-info-wrap">
				<view class="label">机构认证</view>
				<view class="tag">{{ practiceOrg }}</view>
			</view>
			<view class="label-info-wrap">
				<view class="label">擅长领域</view>
				<view class="tag">{{ expertise }}</view>
			</view>
			<view class="label-info-wrap">
				<view class="label">调解数量</view>
				<view class="tag">已调解</view>
				<view class="tag">{{ lengthService }}</view>
			</view>
		</view>

		<view class="mediator-intro">
			<view class="tit">个人简介</view>
			<view class="inner">
				{{ intro ? intro: '暂无简介' }}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        name: '',
				DQBM: '',
				practiceOrg: '',
				expertise: '',
				lengthService: 0,
				intro: '',
				fileId: ''
			};
		},
		onLoad(options) {
			console.log(options);
			this.getMediator(options.id);
		},
		methods: {
			getMediator(id) {
				uni.request({
					url: 'http://shanxi.tunnel.homolo.org/api/apiMediatorService/getMediatorInfo', //仅为示例，并非真实接口地址。
					method: 'GET',
					data: {
						id
					},
					success: (res) => {
						console.log(res);
						const { $displays } = res.data.result;
						this.name = $displays.name;
						this.DQBM = $displays.DQBM;
						this.practiceOrg = $displays.practiceOrg;
						this.expertise = $displays.expertise;
						this.lengthService = $displays.lengthService;
						this.intro = $displays.intro;
						this.fileId = res.data.result.original.portrait;
					}
				});
			},
			getImgUrl(id) {
				return 'api/service/rest/tk.File/' + id + '/view';
			}
		}
	}
</script>

<style lang="scss">
	.content {
		background-color: $primary;
		padding: 157upx 60upx;
	}

	.mediator-info-box {
		position: relative;
		margin-bottom: 40upx;
		padding: 264upx 60upx 60upx;
		background-color: #fff;

		.head-image {
			position: absolute;
			left: 50%;
			top: -96upx;
			width: 244upx;
			height: 324upx;
			transform: translateX(-50%);
			background-color: #f5f5f5;
		}

		.name {
			text-align: center;
			font-size: 40upx;
			color: $dark-text;
		}

		.label-info-wrap {
			display: flex;
			justify-content: flex-start;
			padding-top: 26upx;

			.label {
				margin-right: 22upx;
				padding: 3upx 8upx;
				font-size: 26upx;
				color: $primary;
				border: 1px solid $primary;
			}

			.tag {
				padding: 4upx 8upx;
				font-size: 26upx;
			}
		}
	}

	.mediator-intro {
		padding: 60upx;
		background-color: #fff;

		.tit {
			color: $dark-text;
			font-size: 34upx;
		}
		
		.inner {
			padding-top: 35upx;
			color: $light-text;
			font-size: 28upx;
		}
	}
</style>

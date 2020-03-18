<template>
	<view class="centent">
		<scroll-view :style="{ height: pageHeight + 'px' }" lower-threshold="100" scroll-y="true" @scrolltolower="scrolltolower">
			<view class="search-wrap">
				<input :focus="!searchShow" v-model="searchStr" @blur="blurInput()" class="input" type="text">
				<view :class="{ 'show': searchShow }" @click="searchTap" class="search-mask">
					<text class="iconfont search icon"></text>
					<text class="text">查找调解员</text>
				</view>
			</view>
			<view class="query-wrap">
				<view class="query-up">
					<view @click="changeArea(item)" v-for="(item, index) in tabList" :key="index" class="query-item">
						<text class="text">{{ item.name }}</text>
						<text class="iconfont drop-down icon"></text>
					</view>
				</view>
				<view class="query-down" :class="{ 'show': showModal }">
					<view class="query-scroll">
						<view v-for="(item, index) in checkoption" :key="index" class="bottom-border query-list">
							<view @click="changeChecked(index)" class="query-box" :class="{ 'active': checked === index }">
								<text class="text">{{ item.name }}</text>
								<text class="iconfont xingzhuang icon"></text>
							</view>
						</view>
					</view>
					<view @click="showModal = false" class="mask"></view>
				</view>
			</view>
			<view class="transverse-line"></view>
			<view class="team-list-wrap">
				<view v-if="teamList.length === 0" class="empty">
					<image class="empty-image" src="../../static/images/State_empty.png" mode="aspectFit"></image>
				</view>
				<view v-else v-for="(item, index) in teamList" :key="index" class="team-list bottom-border">
					<navigator :url="'../mediator/mediator?id='+item.id" class="team-box">
						<image class="team-card-image" :src="getImgUrl(item.original.portrait)" mode="aspectFill"></image>
						<view class="team-info-wrap">
							<view class="card-name">{{ item.$displays.name }}</view>
							<view class="card-label-box">
								<view class="label">调解区域</view>
								<view class="tag">{{ item.$displays.DQBM }}</view>
							</view>
							<view class="card-label-box">
								<view class="label">机构认证</view>
								<view class="tag">{{ item.$displays.guideOrg }}</view>
							</view>
							<view class="card-label-box">
								<view class="label">擅长领域</view>
								<view class="tag">{{ item.$displays.expertise }}</view>
							</view>
							<view class="card-label-box">
								<view class="label">调解数量</view>
								<view class="tag">已调解</view>
								<view class="tag">{{ item.$displays.lengthService }}</view>
							</view>
						</view>
					</navigator>
				</view>
			</view>
		</scroll-view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				teamList: [],
				teamListObj: {
					count: null,
					pageNo: 1,
					pageSize: 10,
					pageCount: null
				},
				checkoption: [],
				showModal: false,
				checked: 0,
				tabList: [{
						name: '区域',
						value: 'area'
					},
					{
						name: '擅长领域',
						value: 'expertise'
					},
					{
						name: '从业年限',
						value: 'agelimit'
					},
				],
				checkName: null,
				areaCheckIndex: 0,
				expertiseCheckIndex: 0,
				agelimitCheckIndex: 0,
				pageHeight: 0,
				searchShow: true,
				searchStr: ''
			};
		},
		created() {
			const res = uni.getSystemInfoSync(); //获取手机可使用窗口高度     api为获取系统信息同步接口
			this.pageHeight = res.windowHeight;
		},
		onShow: function() {
			this.getTeamList(1);
		},
		methods: {
			getTeamList(pageNo) {
				uni.request({
					url: 'http://shanxi.tunnel.homolo.org/api/apiMediatorService/searchMediatorList', //仅为示例，并非真实接口地址。
					method: 'GET',
					data: {
						dqbm: '',
						isBMYYT: false,
						pageNo
					},
					success: (res) => {
						const {
							count,
							pageNo,
							pageSize,
							pageCount,
							result
						} = res.data.result;
						console.log(result);
						this.teamList = [...this.teamList, ...result];
						console.log(this.teamList);
						this.teamListObj = {
							count,
							pageNo,
							pageSize,
							pageCount,
							result
						};
					}
				});
			},
			getImgUrl(id) {
				return '/api/service/rest/tk.File/' + id + '/view';
			},
			changeArea(item) {
				if (item.value === 'area') {
					this.checkoption = [{
							name: '全部',
							id: ''
						},
						{
							name: '西安市',
							id: '1'
						},
						{
							name: '重庆市',
							id: '2'
						},
						{
							name: '昆明市',
							id: '3'
						},
						{
							name: '上海市',
							id: '4'
						}
					];
					this.checked = this.areaCheckIndex;
					this.checkName = 'area';
				} else if (item.value === 'expertise') {
					this.checkoption = [{
							name: '全部',
							id: ''
						},
						{
							name: '医疗纠纷',
							id: '1'
						},
						{
							name: '金融纠纷',
							id: '2'
						},
						{
							name: '婚姻纠纷',
							id: '3'
						}
					];
					this.checked = this.expertiseCheckIndex;
					this.checkName = 'expertise';
				} else {
					this.checkoption = [{
							name: '全部',
							id: ''
						},
						{
							name: '一年',
							id: ''
						},
						{
							name: '两年',
							id: ''
						},
						{
							name: '三年',
							id: ''
						},
					];
					this.checked = this.agelimitCheckIndex;
					this.checkName = 'agelimit';
				}
				this.showModal = true;
			},
			changeChecked(index) {
				if (this.checkName === 'area') {
					this.areaCheckIndex = index;
				} else if (this.checkName === 'expertise') {
					this.expertiseCheckIndex = index;
				} else {
					this.agelimitCheckIndex = index;
				}
				this.showModal = false;
			},
			scrolltolower() {
				this.getTeamList(this.teamListObj.pageNo + 1);
			},
			searchTap() {
				this.searchShow = false;
			},
			blurInput() {
				console.log(this.searchStr);
				if (!this.searchStr) {
					this.searchShow = true;
				}
			}
		}
	}
</script>

<style lang="scss">
	.centent {
		height: 400upx;
		display: flex;
		flex-direction: column;
		background-color: #fff;
	}

	.show {
		display: block !important;
	}

	.mask {
		position: absolute;
		left: 0;
		top: 100%;
		width: 100%;
		height: 100vh;
		background-color: rgba(0, 0, 0, .5);
	}

	.search-wrap {
		position: relative;
		height: 70upx;
		line-height: 70upx;
		margin: 30upx 40upx 0;
		background-color: #f5f5f5;

		.input {
			padding: 0 16upx;
			width: 100%;
			height: 100%;
		}

		.search-mask {
			display: none;
			position: absolute;
			left: 0;
			top: 0;
			right: 0;
			bottom: 0;
			text-align: center;

			.icon {
				margin-right: 24upx;
				font-size: 40upx;
				color: $light-text;
				vertical-align: middle;
			}

			.text {
				font-size: 28upx;
				color: $light-text;
				vertical-align: middle;
			}
		}
	}
  
	.empty {
		padding: 300upx 0 0;
		display: flex;
		justify-content: center;
		.empty-image {
			width: 158upx;
			height: 200upx;
		}
	}
	.query-wrap {
		position: relative;
		z-index: 999;

		.query-up {
			padding: 26upx 40upx;
			display: flex;
			justify-content: space-between;
		}

		.query-item {
			padding: 0 18upx;

			.text {
				margin-right: 24upx;
				font-size: 30upx;
				color: $dark-text;
			}

			.icon {
				font-size: 26upx;
				color: #9ea5ba;
			}
		}
	}

	.query-scroll {
		position: relative;
		max-height: 500upx;
		overflow-y: scroll;
		z-index: 1000;
	}

	.query-down {
		display: none;
		position: absolute;
		left: 0;
		right: 0;
		top: 100%;
		padding: 6upx 40upx;
		background-color: #fff;

		.query-box {
			display: flex;
			justify-content: space-between;
			padding: 20upx 0;
			color: $dark-text;
			font-size: 30upx;

			.icon {
				display: none;
			}

			&.active {
				color: $primary;

				.icon {
					display: block;
				}
			}

		}
	}

	.transverse-line {
		height: 10upx;
		background-color: #f5f5f5;
	}

	.team-list-wrap {
		padding-left: 40upx;
	}

	.team-list {
		padding: 40upx 0;
	}

	.team-box {
		display: flex;

		.team-card-image {
			width: 208upx;
			height: 288upx;
			background-color: #888888;
		}

		.team-info-wrap {
			flex: 1;
			padding-left: 40upx;
		}

		.card-name {
			padding-bottom: 8upx;
			line-height: 1;
			font-size: 40upx;
			color: $dark-text;
		}

		.card-label-box {
			padding-top: 18upx;
			display: flex;
			justify-content: flex-start;
		}

		.label {
			margin-right: 26upx;
			padding: 3upx 8upx;
			font-size: 24upx;
			color: #9ea5ba;
			border: 1px solid #9ea5ba;
		}

		.tag {
			padding: 4upx 8upx;
			color: $dark-text;
			font-size: 24upx;
		}
	}
</style>

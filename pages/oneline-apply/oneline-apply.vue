<template>
	<view class="content">
		<view class="form-title">纠纷事项</view>
		<view class="form-item bottom-border active">
			<picker @change="disputeTypeChange" :range="disputeTypeOption" :value="disputeTypeIndex" range-key="name">
				<label class="form-item-ceil">
					<view class="item-ceil-left">纠纷类型</view>
					<view class="item-ceil-db">
						<view v-if="disputeTypeOption[disputeTypeIndex]" class="uni-input">{{ disputeTypeOption[disputeTypeIndex].name }}</view>
						<view class="iconfont arrow-right icon"></view>
					</view>
				</label>
			</picker>
		</view>
		<view class="form-item bottom-border active">
			<picker mode="multiSelector" @change="cityPickerChange" @columnchange="cityPickerColumnChange" :range="addressOptions" :value="addresIndex" range-key="name">
				<label class="form-item-ceil">
					<view class="item-ceil-left">案发区域</view>
					<view class="item-ceil-db">
						<view v-if="addressData.length > 0" class="uni-input">{{ addressText }}</view>
						<view class="iconfont arrow-right icon"></view>
					</view>
				</label>
			</picker>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">详细地址</view>
				<view class="item-ceil-db">
					<input v-model="formData.addressDetail" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">纠纷描述</view>
				<view class="item-ceil-db">
					<input v-model="formData.disputeDesc" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item active">
			<label class="form-item-ceil">
				<view class="item-ceil-left file-item">
					<view>上传附件</view>
					<view class="explain">上传视频，以描述你的纠纷</view>
				</view>
				<view class="item-ceil-db"><view class="iconfont arrow-right icon"></view></view>
			</label>
		</view>
		<view class="form-title">被申请人</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">姓名</view>
				<view class="item-ceil-db">
					<input v-model="formData.respondentName" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input v-model="formData.respondentTel" type="number" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-title">申请人</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">姓名</view>
				<view class="item-ceil-db">
					<input v-model="formData.proposerName" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input v-model="formData.proposerTel" type="number" class="uni-input" placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item">
			<label class="form-item-ceil">
				<view class="item-ceil-left">验证码</view>
				<view class="item-ceil-db">
					<input v-model="formData.code" class="uni-input" placeholder="手机验证码" />
					<view @click="getCode" class="go-code" :class="{ 'disabled': flag }">{{ codeText }}</view>
				</view>
			</label>
		</view>
		<view class="btn-wrap">
			<button @click="submit" type="primary" class="btn">提交</button>
		</view>
	</view>
</template>
<script>
export default {
	data() {
		return {
			disputeTypeOption: [],
			disputeTypeIndex: 0,
			addresIndex: [0, 0, 0],
			pickerText: '',
			codeText: '获取验证码',
			flag: false,
			addressModal: false,
			addressData: [],
			addressOptions: [[], [], []],
			formData: {
				disputeType: undefined,
				address: undefined,
				addressDetail: undefined,
				disputeDesc: undefined,
				respondentName: undefined,
				respondentTel: undefined,
				proposerName: undefined,
				proposerTel: undefined,
				code: undefined
			}
		};
	},
	onLoad() {
		this.formData = { ...uni.getStorageSync('storage_key') };
		this.getAddressData();
		this.getDisputeType();
	},
	computed: {
		addressText() {
			const { addressData, addresIndex } = this;
			const provinceName = addressData[addresIndex[0]].name;
			const cityName = addressData[addresIndex[0]].children[addresIndex[1]].name;
			const districtName = addressData[addresIndex[0]].children[addresIndex[1]].children[addresIndex[2]].name;
			return provinceName + ',' + cityName + ',' + districtName;
		}
	},
	methods: {
		disputeTypeChange(e) {
			console.log(e);
			this.disputeTypeIndex = e.target.value;
			this.formData.disputeType = this.disputeTypeOption[e.target.value].value;
		},
		getDisputeType() {
			uni.request({
				url: 'http://shanxi.tunnel.homolo.org/service/rest/mediation.Stb/6d936e2d7ca94bf8a887619f3022ff4d/getCategory',
				data: {},
				method: 'GET',
				success: res => {
					this.disputeTypeOption = [...res.data.children].map(item => {
						return {
							name: item.name,
							value: item.id
						};
					});
					this.dataPlayback();
				}
			});
		},
		submit() {
			console.log(this.formData);
			const volid = this.formValidation();
			if (!volid) {
				return;
			}
			uni.setStorageSync('storage_key', this.formData);
			console.log(this.formData);
			uni.showToast({
				title: '提交成功',
				success: () => {
					setTimeout(() => {
						uni.navigateBack({
							delta: 1
						})
					}, 1000)
				}
			});
		},
		formValidation() {
			const { formData } = this;
			const validation = new Map([
				['disputeType', '纠纷类型'],
				['address', '案发区域'],
				['addressDetail', '详情地址'],
				['disputeDesc', '纠纷描述'],
				['respondentName', '被申请人姓名'],
				['respondentTel', '被申请人联系电话'],
				['proposerName', '申请人姓名'],
				['proposerTel', '申请人电话'],
				['code', '验证码']
			]);
			for (let [key, value] of validation.entries()) {
				if (!formData[key]) {
					uni.showToast({
						icon: 'none',
						title: validation.get(key) + '不能为空'
					});
					return false;
				}
			}
			return true;
		},
		cityPickerChange(e) {
			console.log(e);
			const { value } = e.detail;
			console.log(value);
			const { addressOptions } = this;
			this.formData.address = [
				addressOptions[0][value[0]].code, 
				addressOptions[1][value[1]].code, 
				addressOptions[2][value[2]].code
				];
		},
		getCode() {
			if (this.flag) {
				return;
			}
			this.flag = true;
			uni.showModal({
				content: '验证码已发送，请注意查收',
				showCancel: false,
				confirmColor: '#056efe',
				success: () => {
					let countIndex = 10;
					const _this = this;
					function codeText() {
						if (countIndex < 0) {
							_this.flag = false;
							_this.codeText = '重新获取';
							return;
						}
						_this.codeText = countIndex + '后重新获取';
						setTimeout(() => {
							countIndex--;
							codeText();
						}, 1000);
					}
					codeText();
				}
			});
		},
		dataPlayback() {
			const { disputeType, address } = this.formData;
			if (disputeType) {
				const index = this.disputeTypeOption.findIndex(item => item.value === disputeType);
				this.disputeTypeIndex = index;
				console.log(this.disputeTypeOption);
			} else {
				this.disputeTypeIndex = 0;
				this.formData.disputeType = this.disputeTypeOption[0].value;
			}
		},
		getAddressData() {
			uni.request({
				url: 'http://shanxi.tunnel.homolo.org/service/rest/tk.Zone/086/tree',
				method: 'GET',
				success: res => {
					const dataArr = ['710000', '810000', '820000'];
					const addressNormal = res.data.children.filter(item => !dataArr.includes(item.code));
					const addressAbnormal = res.data.children
					.filter(item => dataArr.includes(item.code)).map(item => {
						return {
							...item,
							children: [ 
								{ 
									...item,
									children: [
										{ ...item }
									]
								}
							],
						};
					});
					this.addressData = [...addressNormal, ...addressAbnormal];
					this.addressBackPlay();
				}
			});
		},
		addressBackPlay() {
			const { disputeType, address } = this.formData;
			if (address) {
				console.log(this.addressData);
				const firstIndex = this.addressData.findIndex(item => item.code === address[0]);
				console.log(firstIndex);
				const twoIndex = this.addressData[firstIndex].children.findIndex(item => item.code === address[1]);
				const threeIndex = this.addressData[firstIndex].children[twoIndex].children.findIndex(item => item.code === address[2]);
				this.addresIndex = [firstIndex, twoIndex, threeIndex];
			}
			const { addresIndex, addressData } = this;
			this.addressOptions = [
				addressData, 
				addressData[addresIndex[0]].children,
				addressData[addresIndex[0]].children[addresIndex[1]].children
			];
			this.formData.address = [
				this.addressOptions[0][addresIndex[0]].code, 
				this.addressOptions[1][addresIndex[1]].code, 
				this.addressOptions[2][addresIndex[2]].code
			];
		},
		cityPickerColumnChange(e) {
			console.log(e);
			const { column, value } = e.detail;
			const { addressData, addresIndex } = this;
			addresIndex[column] = value;
			if (column === 0) {
				addresIndex[1] = 0;
				addresIndex[2] = 0;
			} else if (column === 1) {
				addresIndex[2] = 0;
			}
			this.addresIndex = [...addresIndex];
			const cityList = addressData[addresIndex[0]].children;
			const districtList = addressData[addresIndex[0]].children[addresIndex[1]].children;
			this.addressOptions = [
				addressData, 
				cityList ? cityList : [], 
				districtList ? districtList: []
			];
		}
	}
};
</script>

<style lang="scss">
.content {
	background-color: #f5f5f5;
}

.form-title {
	padding: 40upx 32upx 16upx;
	font-size: 26upx;
	color: $light-text;
}

.form-item {
	padding: 0 30upx;
	background-color: #fff;

	&.active:active {
		background-color: rgba(#000, 0.1);
	}
}
.form-item-ceil {
	display: flex;

	.item-ceil-left {
		width: 228upx;
		padding: 25upx 0;
		font-size: 32upx;
		color: $light-text;
		&.file-item {
			width: calc(100% - 46upx);
		}
		.explain {
			font-size: 26upx;
		}
	}

	.item-ceil-db {
		flex: 1;
		display: flex;
		font-size: 32upx;

		.uni-input {
			flex: 1;
			padding: 25upx 0;
		}

		.icon {
			display: flex;
			align-items: center;
			width: 46upx;
			padding: 25upx 0;
			font-size: 36upx;
			color: #c7c7cc;
			text-align: right;
		}

		.go-code {
			padding: 25upx 0;
			font-size: 32upx;
			color: $primary;
			&.disabled {
				color: #999;
				cursor: not-allowed;
			}
		}
	}
}

.btn-wrap {
	padding: 80upx 40upx;

	.btn {
		background-color: $primary;
	}
}
</style>

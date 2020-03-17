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
			<label @click="openAddres" class="form-item-ceil">
				<view class="item-ceil-left">案发区域</view>
				<view class="item-ceil-db">
					<view class="uni-input">{{ pickerText }}</view>
					<view class="iconfont arrow-right icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">详细地址</view>
				<view class="item-ceil-db">
					<input v-model="formData.addressDetail" class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">纠纷描述</view>
				<view class="item-ceil-db">
					<input v-model="formData.disputeDesc" class="uni-input" focus placeholder="详情信息" />
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
					<input v-model="formData.respondentName" class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input v-model="formData.respondentTel" type="number" class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-title">申请人</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">姓名</view>
				<view class="item-ceil-db">
					<input v-model="formData.proposerName" class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input v-model="formData.proposerTel" type="number" class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item">
			<label class="form-item-ceil">
				<view class="item-ceil-left">验证码</view>
				<view class="item-ceil-db">
					<input v-model="formData.code" class="uni-input" focus placeholder="详情信息" />
					<view @click="getCode" class="go-code">{{ codeText }}</view>
				</view>
			</label>
		</view>
		<view class="btn-wrap"><button @click="submit" type="primary" class="btn">提交</button></view>
		<simple-address ref="simpleAddress" :pickerValueDefault="cityPickerValueDefault" @onConfirm="onConfirm" themeColor="#007AFF"></simple-address>
	</view>
</template>
<script>
import simpleAddress from '@/components/simple-address/simple-address.nvue';
import areaData from '@/components/simple-address/city-data/area.js';
import cityData from '@/components/simple-address/city-data/city.js';
import provinceData from '@/components/simple-address/city-data/province.js';
export default {
	components: {
		simpleAddress
	},
	data() {
		return {
			disputeTypeOption: [],
			disputeTypeIndex: 0,
			cityPickerValueDefault: [0, 0, 1],
			pickerText: '',
			codeText: '获取验证码',
			flag: false,
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
		this.getDisputeType();
		this.formData = { ...uni.getStorageSync('storage_key') };
		console.log(this.formData);
	},
	methods: {
		bindPickerChange(e) {
			console.log('picker发送选择改变，携带值为', e.target.value);
			this.index = e.target.value;
		},
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
			const volid = this.formValidation();
			if(!volid) {
				return
			}
			uni.setStorageSync('storage_key', this.formData);
			console.log(this.formData);
		  uni.showToast({
		      title: '提交成功',
		      duration: 2000
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
				if(!formData[key]) {
					uni.showToast({
						icon: 'none',
						title: validation.get(key) + '不能为空'
					});
					return false;
				}
			}
			return true;
		},
		openAddres() {
			this.$refs.simpleAddress.open();
		},
		onConfirm(e) {
			this.pickerText = e.label;
			this.formData.address = e.areaCode;
		},
		getCode() {
			uni.showModal({
				title: '验证码已发送，请注意查收',
				content: null,
				showCancel: false,
				success: () => {
					if (this.flag) {
						return;
					}
					this.flag = true;
					let countIndex = 10;
					const _this = this;
					function codeText() {
						if (countIndex < 0) {
							_this.flag = false;
							_this.codeText = '重新获取';
							return;
						}
						setTimeout(() => {
							_this.codeText = countIndex + '后重新获取';
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
			if (address) {
				console.log(address.substring(0, 2));
				const provinceCode = address.substring(0, 2);
				const cityCode = address.substring(0, 4);
				const areaCode = address;
				console.log(areaData);
				const provinceNum = provinceData.findIndex(item => item.value === provinceCode);
				const cityNum = cityData[provinceNum].findIndex(item => item.value === cityCode);
				const areaNum = areaData[provinceNum][cityNum].findIndex(item => item.value === areaCode);
				this.cityPickerValueDefault = [provinceNum, cityNum, areaNum];
				console.log(this.cityPickerValueDefault);
				this.pickerText = provinceData[provinceNum].label + '-' + cityData[provinceNum][cityNum].label + '-' + areaData[provinceNum][cityNum][areaNum].label;
			} else {
				this.formData.address = '110101';
				this.pickerText = provinceData[0].label + '-' + cityData[0][0].label + '-' + areaData[0][0][1].label;
			}
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

<template>
	<view class="content">
		<view class="form-title">纠纷事项</view>
		<view class="form-item bottom-border active">
			<picker @change="bindPickerChange" :value="index" :range="array">
				<label class="form-item-ceil">
					<view class="item-ceil-left">纠纷类型</view>
					<view class="item-ceil-db">
						<view class="uni-input">{{array[index]}}</view>
						<view class="iconfont arrow-right icon"></view>
					</view>
				</label>
			</picker>
		</view>
		<view class="form-item bottom-border active">
			<picker @change="bindPickerChange" :value="index" :range="array">
				<label class="form-item-ceil">
					<view class="item-ceil-left">案发区域</view>
					<view class="item-ceil-db">
						<view class="uni-input">{{array[index]}}</view>
						<view class="iconfont arrow-right icon"></view>
					</view>
				</label>
			</picker>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">详细地址</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">纠纷描述</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-title">被申请人</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">姓名</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-title">申请人</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">姓名</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">联系电话</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="icon"></view>
				</view>
			</label>
		</view>
		<view class="form-item bottom-border">
			<label class="form-item-ceil">
				<view class="item-ceil-left">验证码</view>
				<view class="item-ceil-db">
					<input class="uni-input" focus placeholder="详情信息" />
					<view class="go-code">获取验证码</view>
				</view>
			</label>
		</view>
		<view class="btn-wrap">
			<button type="primary" class="btn">提交</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				array: ['中国', '美国', '巴西', '日本'],
				index: 0
			};
		},
		methods: {
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.target.value)
				this.index = e.target.value
			}
		}
	}
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
			background-color: rgba(#000, .1);
		}
	}

	.form-item-ceil {
		display: flex;

		.item-ceil-left {
			width: 228upx;
			padding: 25upx 0;
			font-size: 32upx;
			color: $light-text;
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

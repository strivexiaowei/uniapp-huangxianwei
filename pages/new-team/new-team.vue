<template>
	<view class="content">
		<view class="page-tobar">
			<uni-search-bar placeholder="查找调解员" bgColor="#f5f5f5" radius="0" @confirm="search" @cancel="cancel"></uni-search-bar>
			<view class="sl-filter-wrap">
				<sl-filter :ref="'slFilter'" :independence="true" :color="titleColor" :themeColor="themeColor" :menuList.sync="menuList" @result="result"></sl-filter>
			</view>
		</view>
		<view class="transverse-line"></view>
		<mescroll-uni top="204" ref="mescrollRef" @init="mescrollInit" @down="downCallback" :up="upOption" @up="upCallback">
			<view class="team-list-wrap">
				<view v-for="(item, index) in dataList" :key="index" class="team-list bottom-inner">
					<navigator :url="'../mediator/mediator?id=' + item.id" class="team-box">
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
		</mescroll-uni>
	</view>
</template>
<script>
import slFilter from '@/components/sl-filter/filter-view.vue';
import MescrollMixin from '@/components/mescroll-uni/mescroll-mixins.js';
import MescrollUni from '@/components/mescroll-uni/mescroll-uni.vue';
import uniSearchBar from '@/components/uni-search-bar/uni-search-bar.vue';
import HMfilterDropdown from '@/components/HM-filterDropdown/HM-filterDropdown.vue';
export default {
	mixins: [MescrollMixin], // 使用mixin
	components: {
		HMfilterDropdown,
		slFilter,
		MescrollUni,
		uniSearchBar
	},
	data() {
		return {
			ddownOption: {
				use: true, // 是否启用下拉刷新; 默认true
				auto: true // 是否在初始化完毕之后自动执行下拉刷新的回调; 默认true
			},
			upOption: {
				page: {
					num: 0,
					size: 10
				},
				noMoreSize: 5, // 配置列表的总数量要大于等于5条才显示'-- END --'的提示
				empty: {
					tip: '暂无相关数据'
				}
			},
			// 列表数据
			dataList: [],
			themeColor: '#056efe',
			titleColor: '#333333',
			name: '',
			checkOutData: {
				dqbm: ''
			},
			menuList: [
				{
					title: '区域',
					key: 'dqbm',
					isSort: true,
					reflexTitle: true,
					detailList: []
				},
				{
					title: '擅长领域',
					key: 'expertise',
					isSort: true,
					reflexTitle: true,
					detailList: []
				},
				{
					title: '从业年限',
					key: 'lengthService',
					isSort: true,
					reflexTitle: true,
					detailList: [
						{
							title: '全部',
							value: ''
						},
						{
							title: '一年',
							value: '1'
						},
						{
							title: '二年',
							value: '2'
						},
						{
							title: '三年',
							value: '3'
						}
					]
				}
			]
		};
	},
	methods: {
		getAreaOptions() {
			uni.request({
				url: 'http://shanxi.tunnel.homolo.org/service/rest/tk.Zone/610000/tree',
				method: 'GET',
				success: res => {
					console.log(res);
					this.menuList[0].detailList = [
						{ title: '全部', value: '' },
						...res.data.children.map(item => {
							return { title: item.name, value: item.code };
						})
					];
					this.$refs.slFilter.resetMenuList(this.menuList);
				}
			});
		},
		getExpertiseOptions() {
			uni.request({
				url: 'http://shanxi.tunnel.homolo.org/service/rest/mediation.Stb/6d936e2d7ca94bf8a887619f3022ff4d/getCategory',
				method: 'GET',
				success: res => {
					console.log(res);
					this.menuList[1].detailList = [
						{ title: '全部', value: '' },
						...res.data.children.map(item => {
							return { title: item.name, value: item.id };
						})
					];
					this.$refs.slFilter.resetMenuList(this.menuList);
				}
			});
		},
		getImgUrl(id) {
			return '/api/service/rest/tk.File/' + id + '/view';
		},
		downCallback() {
			this.mescroll.resetUpScroll();
		},
		/*上拉加载的回调*/
		upCallback(page) {
			let pageNum = page.num; // 页码, 默认从1开始
			let pageSize = page.size; // 页长, 默认每页10条
			const { name, checkOutData } = this;
			uni.request({
				url: 'http://shanxi.tunnel.homolo.org/api/apiMediatorService/searchMediatorList',
				method: 'GET',
				data: {
					...checkOutData,
					name,
					isBMYYT: false,
					pageNo: pageNum
				},
				success: res => {
					const { result, count, pageCount, pageNo, pageSize } = res.data.result;
					// 接口返回的当前页数据列表 (数组)
					let curPageData = result;
					// // 接口返回的当前页数据长度 (如列表有26个数据,当前页返回8个,则curPageLen=8)
					let curPageLen = result.length;
					// // 接口返回的总页数 (如列表有26个数据,每页10条,共3页; 则totalPage=3)
					let totalPage = pageCount;
					// // 接口返回的总数据量(如列表有26个数据,每页10条,共3页; 则totalSize=26)
					let totalSize = count;
					// // 接口返回的是否有下一页 (true/false)
					let hasNext = curPageLen < pageSize;
					// //设置列表数据
					if (page.num == 1) this.dataList = []; //如果是第一页需手动置空列表
					this.dataList = this.dataList.concat(curPageData); //追加新数据
					// // 请求成功,隐藏加载状态
					// //方法一(推荐): 后台接口有返回列表的总页数 totalPage
					this.mescroll.endByPage(curPageLen, totalPage);

					// 如果数据较复杂,可等到渲染完成之后再隐藏下拉加载状态: 如
					this.$nextTick(() => {
						this.mescroll.endSuccess(curPageLen);
					});
				},
				fail: () => {
					//  请求失败,隐藏加载状态
					this.mescroll.endErr();
				}
			});
		},
		result(val) {
			console.log(val);
			this.checkOutData = { ...this.checkOutData, ...val };
			console.log(this.checkOutData);
			this.mescroll.resetUpScroll(true);
		},
		search(e) {
			console.log(e);
			this.name = e.value;
			this.mescroll.resetUpScroll(true);
		},
		cancel() {
			this.name = '';
			this.mescroll.resetUpScroll(true);
		}
	},
	created() {
		// this.$refs.slFilter.resetMenuList(this.menuList);
		this.getAreaOptions();
		this.getExpertiseOptions();
	}
};
</script>

<style lang="scss">
.page-tobar {
	padding: 0 20upx;
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
		overflow: hidden;
	}

	.card-name {
		padding-bottom: 8upx;
		line-height: 1;
		font-size: 40upx;
		color: $dark-text;
	}

	.card-label-box {
		padding-top: 18upx;
		padding-right: 18upx;
		display: flex;
		justify-content: flex-start;
	}

	.label {
		margin-right: 26upx;
		padding: 3upx 8upx;
		font-size: 24upx;
		color: #9ea5ba;
		border: 1px solid #cfe4ff;
	}

	.tag {
		flex: 1;
		padding: 4upx 8upx;
		color: $dark-text;
		font-size: 24upx;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
}
</style>

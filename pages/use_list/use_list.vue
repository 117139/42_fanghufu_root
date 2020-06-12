<template>
	<view class="content">
		<cu-custom bgColor="bg-blue" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">使用记录</block>
		</cu-custom>
		<view v-if="datas.length==0" class="zanwu" style="line-height: 100px;">暂无数据</view>
		<!-- <view  class="zanwu fz30 c9" style="line-height: 100px;">暂无数据</view> -->
		<view class="jl_li" v-for="(item,index) in datas">
			<view class="jl1">
				<view>产品名:{{item.title}}</view>
			</view>
			<view class="jl1">
				<view>生产单位:{{item.production_unit}}</view>
			</view>
			<view class="jl1">
				<view>使用时间:{{item.use_time}}</view>
				<view></view>
			</view>
			<view class="jl1">
				<view>尺码:{{item.v_size}}</view>
				<view></view>
			</view>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	import {
		mapState
	} from 'vuex'
	
	export default {
		computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
		data() {
			return {
				btnkg:0,
				page:1,
				size:20,
				datas:[]
			}
		},
		onLoad(option) {
			var that = this
			console.log(option)
			that.getdata()
		},
		onPullDownRefresh(){
			var that =this
			that.page=1
			that.datas=[]
			that.getdata()
		},
		onReachBottom(){
			var that =this
			that.getdata()
		},
		methods: {
			jump(e) {
				var that = this
				if (that.btnkg == 1) {
					return
				} else {
					that.btnkg = 1
					setTimeout(function() {
						that.btnkg = 0
					}, 1000)
				}
				console.log(e.currentTarget.dataset.url)
				uni.navigateTo({
					url: e.currentTarget.dataset.url,
				});
			},
			getdata() {
				var that = this
				///goods/record
				var jkurl = '/user/useRecord'
				var data = {
					token: uni.getStorageSync('loginmsg').userToken,
					page: that.page,
					size: that.size
				}
				service.get(jkurl, data,
					function(res) {
						that.btnkg = 0
						if (res.data.code == 1) {
			
							if(that.page==1){
								that.datas = res.data.data
							}else{
								if(!res.data.data){
									uni.showToast({
										icon:'none',
										title:'暂无更多数据'
									})
								}
								that.datas = that.datas.concat(res.data.data)
							}
							
							that.page++
						} else {
							if (res.data.msg) {
								uni.showToast({
									icon: 'none',
									title: res.data.msg
								})
							} else {
								uni.showToast({
									icon: 'none',
									title: '操作失败'
								})
							}
						}
					},
					function(err) {
						that.btnkg = 0
						if (err.data.msg) {
							uni.showToast({
								icon: 'none',
								title: err.data.msg
							})
						} else {
							uni.showToast({
								icon: 'none',
								title: '操作失败'
							})
						}
					}
				)
			
			},
		}
	}
</script>

<style scoped>
	.content{
		background: #fff;
	}
	.jl_li{
		width: 100%;
		color: #333;
		border-bottom: 1upx solid #ddd;
		padding: 20upx;
		-webkit-box-sizing: border-box;
		-moz-box-sizing: border-box;
		box-sizing: border-box;
	}
	.jl1{
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 5upx 0;
	}
</style>

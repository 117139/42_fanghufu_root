<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">基本信息</block>
		</cu-custom>
		<image class="index_bg" src="../../static/img/images/index1bg_01.jpg"></image>
		<view class="index_box">

			<view class="hello">
				<view class="title">
					<view class="cp_name">产品名称:{{datas.title}}</view>
					<view class="cp_msg">
						<view>生产日期：{{datas.production_time}}</view>
						<view>产品规格:{{datas.size}}</view>
						<view>当前使用次数:{{datas.use_number}}次</view>
						<view>生产单位：{{datas.production_unit}}</view>
						<view>使用次数:{{datas.may_use_number}}次</view>
						<view>使用状态:{{datas.v_status==1?'正常':'已报废'}}</view>
					</view>

				</view>
			</view>
		</view>
		<view class="msg_list">
			<view class="list_tit">
				<image src="../../static/img/msgbg_03.png"></image>
				<view class="tit_box"><text></text>使用记录</view>
			</view>
			<scroll-view class="jl_list" scroll-y>
				<view v-if="datas.record_data.length==0" class="zanwu">暂无记录</view>
				<view class="jl_li" v-for="(item,index) in datas.record_data">
					<view class="jl_d1">{{item.init}}</view>
					<view class="jl_d2">{{item.use_time}}</view>
					<view class="jl_d3">{{item.real_name}}</view>
				</view>
				<view class="baofei_li " v-if="datas.v_scrap_content">
					<view class="tit_box"><text></text>报废备注</view>
					<view class="jl_d2"></view>
					<view  class="jl_d3">{{datas.v_scrap_content}}</view>
				</view>
			</scroll-view>
		</view>
		<view class="cz_box" v-if="datas.v_status==1">
			<view @tap="apply">申请使用</view>
			<view class="bf" @tap="scrap">报废</view>
		</view>
		<view v-else class="cz_box">
			<view class="ybf">已报废</view>
		</view>
		<view class="tk_text" v-if="tk_show">
			<view class="tk_czz" @tap="tk_show=false"></view>
			<textarea placeholder="请填写报废备注..." focus @blur="tk_show=false" cursor-spacing='130' v-model="liyou" maxlength="500" @confirm="baofei"></textarea>
		  <view  class="tk_btn">
		  	<view @tap="baofei">提交</view>
		  </view>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	import {
		mapState,
		mapMutations
	} from 'vuex'

	export default {
		computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
		data() {
			return {
				btnkg: 0,
				top: '50',
				datas: [],
				data_list: [],
				liyou:'',
				tk_show:false,
			}
		},
		onLoad(option) {
			var that = this
			console.log(option)
			that.type = option.type
			that.goods_sn = option.goods_sn
			that.getdata()
			//      if (!this.hasLogin) {
			//          uni.showModal({
			//              title: '未登录',
			//              content: '您未登录，需要登录后才能继续',
			//              /**
			//               * 如果需要强制登录，不显示取消按钮
			//               */
			//              showCancel: !this.forcedLogin,
			//              success: (res) => {
			//                  if (res.confirm) {
			// /**
			//  * 如果需要强制登录，使用reLaunch方式
			//  */
			//                      if (this.forcedLogin) {
			//                          uni.reLaunch({
			//                              url: '../login/login'
			//                          });
			//                      } else {
			//                          uni.navigateTo({
			//                              url: '../login/login'
			//                          });
			//                      }
			//                  }
			//              }
			//          });
			//      }
		},
		onShow() {
			this.btnkg = 0
		},
		methods: {
			onShareAppMessage: function(e) {
				let title = '邦维高科特种纺织品'
				return {
					title: title,
					path: 'pages/main_jb/main_jb'
				}
			},
			bindLogin() {
				uni.navigateTo({
					url: '../login/login',
				});
			},

			jump(e) {
				console.log(e.currentTarget.dataset.url)
				uni.navigateTo({
					url: e.currentTarget.dataset.url,
				});
			},
			apply() {
				///goods/apply
				var that = this
				///goods/record
				if(!that.hasLogin){
					uni.navigateTo({
						url: '../login/login',
					});
					return
				}
				uni.showModal({
					title: '提示',
					content: '是否申请使用该防护服',
					success: function(res) {
						if (res.confirm) {
							
							// console.log('用户点击确定');
							var jkurl = '/goods/apply'
							var data = {
								token: uni.getStorageSync('loginmsg').userToken,
								type: that.type,
								goods_sn: that.goods_sn
							}
							if (that.btnkg == 1) {
								return
							} else {
								that.btnkg = 1

							}
							service.post(jkurl, data,
								function(res) {
									that.btnkg = 0
									if (res.data.code == 1) {

										uni.showToast({
											icon: 'none',
											title: '申请已成功'
										})
										setTimeout(function() {
											that.getdata()
										}, 1000)

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

						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});

			},
			//申请报废
			scrap() {
				var that = this
				if(!that.hasLogin){
					uni.navigateTo({
						url: '../login/login',
					});
					return
				}
				uni.showModal({
					title: '提示',
					content: '是否申请报废该防护服',
					success: function(res) {
						if (res.confirm) {
							that.tk_show=true
							// return
							// console.log('用户点击确定');
							///goods/scrap
							

						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});

			},
			baofei(){
				var that=this
				var jkurl = '/goods/scrap'
				if(!that.liyou){
					uni.showToast({
						icon:'none',
						title:'请填写报废备注'
					})
				}
				var data = {
					token: uni.getStorageSync('loginmsg').userToken,
					goods_sn: that.goods_sn,
					scrap_content:that.liyou
				}
				if (that.btnkg == 1) {
					return
				} else {
					that.btnkg = 1
				
				}
				service.post(jkurl, data,
					function(res) {
						that.btnkg = 0
						if (res.data.code == 1) {
							that.tk_show=false
							that.liyou=''
							uni.showToast({
								icon: 'none',
								title: '报废成功'
							})
							setTimeout(function() {
								that.getdata()
							}, 1000)
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
			getdata() {
				var that = this
				///goods/record
				var jkurl = '/goods/record'
				var data = {
					type: that.type,
					goods_sn: that.goods_sn
				}
				service.get(jkurl, data,
					function(res) {
						that.btnkg = 0
						if (res.data.code == 1) {


							that.datas = res.data.data
							if (res.data.data.v_status != 1) {
								uni.showModal({
									title: '提示',
									content: '已报废',
									success: function(res) {
										if (res.confirm) {
											console.log('用户点击确定');
										} else if (res.cancel) {
											console.log('用户点击取消');
										}
									}

								})
							}
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
			saoman() {
				// 允许从相机和相册扫码
				uni.scanCode({
					success: function(res) {
						console.log('条码类型：' + res.scanType);
						alert(res.scanType)
						alert(res.result)
						console.log('条码内容：' + res.result);
					}
				});
			}
		}
	}
</script>

<style scoped>
	uni-page {
		border: #F7F7F7;
	}
	.tk_text{
		position: fixed;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background: rgba(0,0,0,.5);
		z-index: 997;
		width: 100%;
		padding: 0 0 28rpx;
		box-sizing: border-box;
	}
	.tk_czz{
		position: absolute;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		z-index: 998;
	}
	.tk_text textarea{
		position: absolute;
		top: 200rpx;
		z-index: 999;
		width: 100%;
		height: 290rpx;
		border-radius: 12rpx;
	  border: 1px solid #eee;
		padding: 12rpx;
		box-sizing: border-box;
		background: #fff;
		font-size: 26rpx;
		color: #333;
	}
	.tk_text view{
		border-bottom-right-radius: 12rpx;
		border-bottom-left-radius: 12rpx;
	  box-sizing: border-box;
	  font-size: 24rpx;
	  color: #666;
	  text-align: right;
	  padding: 0 12rpx 10rpx 0;
	}
	.tk_text .tk_btn{
		width: 100%;
		display: flex;
		justify-content: center;
		position: absolute;
		top: 600rpx;
		z-index: 999;
	}
	.tk_btn view {
		width: 600rpx;
		height: 78rpx;
		background: linear-gradient(-55deg, rgba(81, 95, 235, 1), rgba(63, 129, 253, 1));
		border-radius: 39rpx;
		font-size: 28rpx;
		color: #fff;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.zanwu {
		width: 100%;
		height: 100rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 28rpx;
		color: #999;
	}

	.cu-bar .action {
		color: #fff;
	}

	.hello {
		width: 100%;
		padding-top: 20upx;
		padding-left: 28rpx;
		padding-right: 28rpx;
		box-sizing: border-box;
		display: flex;
		/* flex: 1; */
		flex-direction: column;
		align-items: center;
		height: 391upx;
		box-sizing: border-box;
	}


	.ul {
		font-size: 30upx;
		color: #8f8f94;
		margin-top: 50upx;
	}

	.ul>view {
		line-height: 50upx;
	}

	.content {
		background: #F7F7F7;
	}

	.index_bg {
		width: 100%;
		height: 541upx;
		position: absolute;
		top: 0;
		z-index: 1;
	}

	.index_box {
		position: relative;
		top: 0;
		z-index: 3;
		width: 100%;
		/* min-height: 1323upx; */
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.cp_name {
		font-size: 40rpx;
		color: #fff;
	}

	.title {
		width: 100%;
	}

	.cp_msg {
		width: 100%;
		display: flex;
		flex-wrap: wrap;
	}

	.cp_msg>view {
		width: 50%;
		font-size: 26rpx;
		color: #fff;
		margin-top: 35rpx;
	}

	.msg_list {
		width: 690rpx;
		height: calc(100vh - 541upx);
		box-shadow: 0px 0px 10rpx 0px rgba(0, 0, 0, 0.1);
		margin: 0 auto;
		position: relative;
		top: -94rpx;
		z-index: 20;
		/* background: #fff; */
	}

	.list_tit {
		width: 100%;
		height: 94rpx;
		position: relative;
	}

	.list_tit image {
		width: 100%;
		height: 94rpx;
		position: absolute;
		top: 0;
		z-index: 20;
	}

	.tit_box {
		position: absolute;
		top: 30rpx;
		left: 28rpx;
		padding-left: 15rpx;
		font-size: 30rpx;
		color: #555;
		z-index: 22;
	}

	.tit_box text {
		position: absolute;
		top: 8rpx;
		left: 0;
		width: 4rpx;
		height: 30rpx;
		background: rgba(43, 63, 147, 1);
		z-index: 22;
	}

	.jl_list {
		width: 100%;
		height: calc(100% - 94rpx);
		background: #fff;
		padding: 0 20rpx;
		box-sizing: border-box;
	}

	.jl_li {
		width: 100%;
		height: 107rpx;
		display: flex;
		align-items: center;
		border-bottom: 1px solid #eee;
	}

	.jl_d1 {
		width: 150rpx;
		font-size: 30rpx;
		color: #555;
		font-weight: bold;
	}

	.jl_d2 {
		flex: 1;
		font-size: 24rpx;
		color: #999;
	}

	.jl_d3 {
		width: 150rpx;
		font-size: 30rpx;
		color: #656565;
		text-align: right;
	}
	.baofei_li{
		width: 100%;
	}
	.baofei_li .jl_d1{
		width: 150rpx;
		font-size: 30rpx;
		color: #555;
		font-weight: bold;
		line-height: 40upx;
		margin-bottom: 10px;
	}
	.baofei_li .jl_d3{
		width: 100%;
		font-size: 28rpx;
		color: #555;
		line-height: 34upx;
		text-align: left;
	}
	.baofei_li .tit_box{
		position: relative;
		top: 0;
		left: 0;
		margin-bottom: 10px;
	}
	.cz_box {
		position: fixed;
		z-index: 99;
		bottom: 0;
		width: 100%;
		height: 100rpx;
		background: #fff;
		box-shadow: 0px 1px 10rpx 0px rgba(0, 0, 0, 0.1);
	}

	.cz_box {
		display: flex;
		align-items: center;
		justify-content: space-around;
	}

	.cz_box view {
		width: 277rpx;
		height: 78rpx;
		background: linear-gradient(-55deg, rgba(81, 95, 235, 1), rgba(63, 129, 253, 1));
		border-radius: 39rpx;
		font-size: 28rpx;
		color: #fff;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.cz_box view.bf {
		background: linear-gradient(-55deg, rgba(255, 85, 85, 1), rgba(254, 112, 111, 1));
	}

	.cz_box view.ybf {
		width: 690upx;
		background: #eee;
		color: #333;
	}
	
</style>

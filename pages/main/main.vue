<template>
	<view class="content">
		<!-- <cu-custom bgColor="rgba(0,0,0,0)" :isBack="true">
			    <block slot="content">导航栏</block>
			    <block slot="content"><text class="iconfont iconcaozuo"></text></block>
			</cu-custom> -->
		<image class="index_bg" src="../../static/img/images/indexbg_01.jpg"></image>
		<view class="index_box">
			<view v-if="hasLogin" class="hello">

				<view class="title">
					<!-- #ifdef APP-PLUS -->
					<!-- <view class="my_cz" style="{top:{top}}" @tap="jump" data-url="../main_edit/main_edit"><text class="iconfont iconcaozuo"></text></view> -->
					<view class="my_cz" style="{top:{top}}" @tap="showModal" data-target="Modal"><text class="iconfont iconcaozuo"></text></view>
					<view class="cu-modal" :class="modalName=='Modal'?'show':''" @tap="hideModal">
						<view class="to_my_box">
							<view @tap="jump" data-url="../main_edit1/main_edit1">修改用户信息</view>
							<view @tap="jump" data-url="../use_list/use_list">个人使用记录</view>
							<view @tap="jump" data-url="../main_edit/main_edit">退出</view>
						</view>
					</view>

					<!-- #endif -->
					<view class="tx_box" @tap="jump" data-url="../main_edit1/main_edit1">
						<image :src="imgurl+loginmsg.avatarurl"></image>
						<view class="tx_edit"><text class="iconfont iconbianji"></text></view>
					</view>
					<view class="uname">{{loginmsg.real_name}}</view>
					<view class="uname1">{{loginmsg.unit}}</view>
					<!-- #ifdef MP-WEIXIN -->
					<view class="uname1" @tap="jump" data-url="../use_list/use_list">使用记录>></view>
					<!-- #endif -->
				</view>

			</view>
			<view v-if="!hasLogin" class="hello">
				<view class="title">
					
					<view class="tx_box" @tap="bindLogin">
						<image :src="imgurl+'/static/cms/img/user_logo.png'"></image>
						<view class="tx_edit"><text class="iconfont iconbianji"></text></view>
					</view>
					<view class="uname"> 您好 游客</view>
					<button v-if="!hasLogin" type="primary" class="primary login_btn" @tap="bindLogin">登录</button>

				</view>
			</view>
			<view class="sys">
				<image src="../../static/img/index2.png" @tap="saoman"></image>
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
				modalName: null,
				imgurl: '',
				top: '150',
				loginmsg: ''
			}
		},
		onLoad() {

			var that = this
			that.imgurl = service.imgurl
			if (that.hasLogin) {
				that.loginmsg = uni.getStorageSync('loginmsg')
			}
			var that=this
			// #ifdef APP-PLUS
			var phone=uni.getStorageSync('phone')
			console.log(phone)
			var password=uni.getStorageSync('password')
			if(phone){
				var jkurl = '/login'
				var data = {
					phone: phone,
					password: password,
					type: 1
				}
				service.post(jkurl, data,
					function(res) {
						uni.showToast({
							icon: 'none',
							title: res
						})
						if (res.data.code == 1) {
				
							// uni.showToast({
							// 	icon: 'none',
							// 	title: '登录成功'
							// })
							console.log(res.data.data)
							that.login(res.data.data.real_name);
							uni.setStorageSync('loginmsg', res.data.data)
							that.loginmsg = res.data.data
							uni.setStorageSync('phone', phone)
							uni.setStorageSync('password', password)
							// setTimeout(() => {
							// 	// uni.navigateTo({
							// 	// 	url: '../main/main'
							// 	// }, 1000)
							// 	uni.navigateBack()
							// },1000)
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
						console.log(err)
						that.btnkg=0
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
			}
			// #endif
			// #ifdef MP-WEIXIN
			wx.getSetting({
				success: res => {
					
					if (res.authSetting['scope.userInfo'] == true) {
						// 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
						console.log('已经授权')
						wx.getUserInfo({
							success(res) {
								console.log(res.userInfo)
								var userInfo=res.userInfo
								wx.setStorageSync('userInfo', res.userInfo)
								if (!userInfo) {
			
								} else {
									console.log('有授权')
									wx.login({
										success: function(res) {
											console.log('登录')
											// 发送 res.code 到后台换取 openId, sessionKey, unionId
											var jkurl = '/login'
											var data={
												login_type:1,   ///前端自用判断是否需要跳登录
												type:3,
												wxcode:res.code,
												bdtype:1
											}
											service.post(jkurl, data,
												function(res) {
													
													if (res.data.code == 1) {
											
														// uni.showToast({
														// 	icon: 'none',
														// 	title: '登录成功'
														// })
														uni.setStorageSync('loginmsg', res.data.data)
														that.login(res.data.real_name)
														that.loginmsg = uni.getStorageSync('loginmsg')
														// setTimeout(() => {
														// 	uni.navigateTo({
														// 		url: '../main/main'
														// 	}, 1000)
														// },1000)
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
											
										}
									})
								}
							}
						})
			
					} else {
							
					}
				}
			})
			
			// #endif
		},
		onShow() {
			var that = this
			that.btnkg = 0
			if (that.hasLogin) {
				that.loginmsg = uni.getStorageSync('loginmsg')
			}

		},
		methods: {
			...mapMutations(['login','logout']),
			onShareAppMessage: function(e) {
				let title = '邦维高科特种纺织品'
				return {
					title: title,
					path: 'pages/main/main'
				}
			},
			showModal(e) {
				this.modalName = e.currentTarget.dataset.target
			},
			hideModal(e) {
				this.modalName = null
			},
			bindLogin() {
				uni.navigateTo({
					url: '../login/login',
				});
			},

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
			saoman() {
				// 允许从相机和相册扫码
				uni.scanCode({
					success: function(res) {
						console.log('条码类型：' + res.scanType);
						console.log(res.scanType)
						console.log(res.result)
						console.log('条码内容：' + res.result);
						var ret = res.result

						console.log(typeof ret)
						if (typeof ret == 'string') {
							ret = JSON.parse(ret)
						}
						console.log(res.result.type)
						if (ret.type == 'fhfcode') {
							uni.navigateTo({
								url: '../main_jb/main_jb?type=' + ret.type + '&goods_sn=' + ret.goods_sn,
							});
						} else {
							uni.showToast({
								icon: 'none',
								title: '请选择正确的二维码'
							})
						}

					},
					fail() {
						// uni.navigateTo({
						// 	url: '../main_jb/main_jb',
						// });
						uni.showToast({
							icon: 'none',
							title: '操作失败'
						})
					}
				});
			}
		}
	}
</script>

<style scoped>
	uni-page {
		border: #fff;
	}

	.hello {
		padding-top: 200upx;
		display: flex;
		/* flex: 1; */
		flex-direction: column;
		align-items: center;
		height: 736upx;
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
		background: #fff;
	}

	.index_bg {
		width: 100%;
		height: 736upx;
		position: absolute;
		top: 0;
		z-index: 1;
	}

	.index_box {
		position: relative;
		top: 0;
		z-index: 3;
		width: 100%;
		height: 100vh;
		min-height: 1323upx;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.tx_box {
		width: 165upx;
		height: 165upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		position: relative;
		margin: 0 auto;
	}

	.tx_edit {
		position: absolute;
		bottom: 0;
		right: 0;
		z-index: 9;
		width: 54upx;
		height: 54upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.tx_edit text {
		color: #515FEB;
	}
	.tx_box image {
		width: 155upx;
		height: 155upx;
		border-radius: 50%;
		position: relative;
		z-index: 5;
	}

	.uname {
		font-size: 36upx;
		font-family: PingFang;
		font-weight: bold;
		color: rgba(255, 255, 255, 1);
		margin-top: 30upx;
		text-align: center;
	}

	.uname1 {
		font-size: 28upx;
		font-family: PingFang SC;
		font-weight: 500;
		color: rgba(254, 254, 254, 1);
		text-align: center;
		margin-top: 20upx;
	}

	.login_btn {
		width: 150upx;
		height: 70upx;
		line-height: 70upx;
		font-size: 34upx;
		margin-top: 40upx;
	}

	.sys {
		width: 587upx;
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.sys image {

		width: 587upx;
		height: 587upx;
	}

	.my_cz {
		position: absolute;
		top: 80upx;
		right: 30upx;
		color: #fff;
	}
	.to_my_box{
		position: absolute;
		top: 120upx;
		right: 10px;
		display: inline-block;
		vertical-align: middle;
		margin-left: auto;
		margin-right: auto;
		width: 120px;
		
		/* height: 200upx; */
		max-width: 100%;
		background-color: #f8f8f8;
		border-radius: 4px;
		overflow: hidden;
	}
	.to_my_box view{
		color: #333;
		font-size: 30upx;
		text-align: center;
		height: 100upx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.to_my_box view+view{
		border-top: 1upx solid #eee;
		
	}
</style>

<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">登录</block>
		</cu-custom>
		<image class="loginbg" src="../../static/img/loginbg_03.jpg" mode="aspectFill"></image>
		<view class="login_logo">
			<image src="../../static/img/login_logo.png" mode=""></image>
		</view>
		<view class="login_type">
			<view :class="{'cur':logintype==0}" data-type='0' @tap="loginfuc">账号登陆<text></text></view>
			<view :class="{'cur':logintype==1}" data-type='1' @tap="loginfuc">验证码登录<text></text></view>
		</view>
		<view class="input-group">
			<view class="input-row  border">
				<text class="title iconfont iconshouji" style="width: 60rpx;"></text>
				<input class="m-input" type="number" clearable focus v-model="account" placeholder="请输入账号" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view v-if="logintype==0" class="input-row border" style="margin-top: 20rpx;">
				<text class="title iconfont iconsuo" style="width: 60rpx;"></text>
				<input type="password" displayable v-model="password" placeholder="请输入密码" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view v-if="logintype==1" class="input-row" style="margin-top: 20rpx;">
				<text class="title iconfont iconanquan" style="width: 60rpx;"></text>
				<input type="text" v-model="code" placeholder="请输入验证码" placeholder-style="color:#fff;" placeholder-class="pl_class"></input>
				<view v-if="yzm_type==0" class="getyzm" @tap="getCode">获取验证码</view>
				<view v-if="yzm_type==1" class="getyzm">{{yztime}}s</view>
			</view>
		</view>
		<view class="action-row">
			<navigator url="../reg/reg">马上注册</navigator>
			<navigator url="../pwd/pwd">忘记密码</navigator>
		</view>
		<view class="btn-row">
			<view class="sub_btn" @tap="bindLogin">登录</view>
			<!-- <button type="primary" class="primary" @tap="bindLogin">登录</button> -->
		</view>
		<view class="xieyi">登录表示您同意<navigator url="../xieyi/xieyi">《北京邦维高科特种纺织品有限责任公司 许可协议》</navigator>
		</view>
		<!-- <view class="oauth-row" v-if="hasProvider" v-bind:style="{top: positionTop + 'px'}">
            <view class="oauth-image" v-for="provider in providerList" :key="provider.value">
                <image :src="provider.image" @tap="oauth(provider.value)"></image>
            </view>
        </view> -->
	</view>
</template>

<script>
	import service from '../../service.js';
	import {
		mapState,
		mapMutations
	} from 'vuex'
	import mInput from '../../components/m-input.vue'

	export default {
		components: {
			mInput
		},
		data() {
			return {
				btnkg: 0,
				logintype: '0',
				yzm_type: 0,
				yztime: 60,
				providerList: [],
				hasProvider: false,
				account: '',
				password: '',
				code: '',
				positionTop: 0
			}
		},
		onLoad(options) {
			var that =this
			// if(options.hasLogin==false){
				that.logout()
			// }
			
			// #ifdef MP-WEIXIN
			console.log('开始授权')
			wx.getSetting({
				success: res => {
					console.log(JSON.stringify(res))
					// console.log(res.authSetting['scope.userInfo'])
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
											console.log(res)
											// 发送 res.code 到后台换取 openId, sessionKey, unionId
											var uinfo = userInfo
											
											// var rawData = JSON.stringify(uinfo)
											var jkurl = '/login'
											var data={
												login_type: 1,
												type:3,
												wxcode:res.code,
												bdtype:1
											}
											service.post(jkurl, data,
												function(res) {
													
													if (res.data.code == 1) {
											
														uni.showToast({
															icon: 'none',
															title: '已绑定账号，正在登录'
														})
														that.login(res.data.real_name);
														uni.setStorageSync('loginmsg', res.data.data)
														setTimeout(() => {
															uni.reLaunch({
																url: '../main/main'
															})
														},1500)
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
						wx.navigateTo({
							url:'../login0/login0'
						})
					}
				}
			})
			// #endif
		},
		computed: mapState(['forcedLogin']),
		methods: {
			...mapMutations(['login','logout']),
			initProvider() {
				const filters = ['weixin', 'qq', 'sinaweibo'];
				uni.getProvider({
					service: 'oauth',
					success: (res) => {
						if (res.provider && res.provider.length) {
							for (let i = 0; i < res.provider.length; i++) {
								if (~filters.indexOf(res.provider[i])) {
									this.providerList.push({
										value: res.provider[i],
										image: '../../static/img/' + res.provider[i] + '.png'
									});
								}
							}
							this.hasProvider = true;
						}
					},
					fail: (err) => {
						console.error('获取服务供应商失败：' + JSON.stringify(err));
					}
				});
			},
			initPosition() {
				/**
				 * 使用 absolute 定位，并且设置 bottom 值进行定位。软键盘弹出时，底部会因为窗口变化而被顶上来。
				 * 反向使用 top 进行定位，可以避免此问题。
				 */
				this.positionTop = uni.getSystemInfoSync().windowHeight - 100;
			},
			loginfuc(e) {
				console.log(e.currentTarget.dataset.type)
				var type = e.currentTarget.dataset.type
				var that = this
				console.log(that.logintype)
				if (that.logintype == type) {
					return
				} else {
					that.logintype = type

				}
			},
			getCode() {
				let that = this

				if (that.account == '' || !(/^1\d{10}$/.test(that.account))) {
					wx.showToast({
						icon: 'none',
						title: '手机号有误'
					})
					return
				}
				if (that.btnkg == 1) {
					return
				} else {
					that.btnkg = 1
				}
				// uni.showToast({
				// 	icon: 'none',
				// 	title: '发送成功'
				// })
				// that.codetime()
				// that.btnkg= 0
				// return
				var jkurl = '/sendCode'
				var data = {
					type: 3,
					phone: that.account
				}
				service.get(jkurl, data,
					function(res) {
						that.btnkg=0
						if (res.data.code == 1) {

							uni.showToast({
								icon: 'none',
								title: '发送成功'
							})
							that.codetime()

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
						that.btnkg=0
						if (err.data.message) {
							uni.showToast({
								icon: 'none',
								title: err.data.message
							})
						} else {
							uni.showToast({
								icon: 'none',
								title: '操作失败'
							})
						}
					})

			},
			codetime() {
				let that = this
				let time = 60
				let st = setInterval(function() {
					if (time == 0) {
						that.yzm_type = 0
						clearInterval(st);
					} else {
						let news = time--;
						// console.log(news)
						that.yzm_type = 1
						that.yztime = news

					}
				}, 1000);
			},
			bindLogin() {
				
				var that = this
				// #ifdef MP-WEIXIN
				console.log('开始授权')
				wx.getSetting({
					success: res => {
						console.log(JSON.stringify(res))
						// console.log(res.authSetting['scope.userInfo'])
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
												console.log(res)
												// 发送 res.code 到后台换取 openId, sessionKey, unionId
												var uinfo = userInfo
												
												var rawData = JSON.stringify(uinfo)
												that.m_login(res.code)
												
											}
										})
									}
								}
							})

						} else {
							wx.navigateTo({
								url:'../login0/login0'
							})
						}
					}
				})
				return
				// #endif

				that.m_login()


				/**
				 * 客户端对账号信息进行一些必要的校验。
				 * 实际开发中，根据业务需要进行处理，这里仅做示例。
				 */

				// const validUser = service.getUsers().some(function(user) {
				// 	return data.account === user.account && data.password === user.password;
				// });
				// if (validUser) {
				// 	this.toMain(this.account);
				// } else {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '用户账号或密码不正确',
				// 	});
				// }

			},
			m_login(rescode) {
				var that = this
				if (that.account == '' || !(/^1\d{10}$/.test(that.account))) {
					uni.showToast({
						icon: 'none',
						title: '手机号有误'
					})
					return
				}
				if (that.password.length < 6 && that.logintype == 0) {
					uni.showToast({
						icon: 'none',
						title: '密码最短为 6 个字符'
					});
					return;
				}
				if (!that.code && that.logintype == 1) {
					uni.showToast({
						icon: 'none',
						title: '请输入验证码'
					});
					return;
				}

				var jkurl = '/login'
				var data
				if (that.logintype == 0) {//密码
					data = {
						phone: this.account,
						password: this.password,
						type: 1
					};
					// #ifdef MP-WEIXIN
					data = {
						phone: this.account,
						password: this.password,
						type: 3,
						wxcode: rescode,
						bdtype: 2
					}
					// #endif
				} else { //验证码
					data = {
						phone: this.account,
						code: this.code,
						type: 2
					}
					// #ifdef MP-WEIXIN
					data = {
						phone: this.account,
						code: this.code,
						type: 3,
						wxcode: rescode,
						bdtype: 3
					}
					// #endif
				}
				console.log(data)
				if (that.btnkg == 1) {
					return
				} else {
					that.btnkg = 1
				}
				service.post(jkurl, data,
					function(res) {
						that.btnkg=0
						if (res.data.code == 1) {

							uni.showToast({
								icon: 'none',
								title: '登录成功'
							})
							that.login(res.data.real_name);
							uni.setStorageSync('loginmsg', res.data.data)
							uni.setStorageSync('phone', that.account)
							var phone=uni.getStorageSync('phone')
							console.log(phone)
							uni.setStorageSync('password', that.password)
							setTimeout(() => {
								// uni.navigateTo({
								// 	url: '../main/main'
								// }, 1000)
								uni.navigateBack()
							},1000)
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

			},
			oauth(value) {
				uni.login({
					provider: value,
					success: (res) => {
						uni.getUserInfo({
							provider: value,
							success: (infoRes) => {
								/**
								 * 实际开发中，获取用户信息后，需要将信息上报至服务端。
								 * 服务端可以用 userInfo.openId 作为用户的唯一标识新增或绑定用户信息。
								 */
								this.toMain(infoRes.userInfo.nickName);
							}
						});
					},
					fail: (err) => {
						console.error('授权登录失败：' + JSON.stringify(err));
					}
				});
			},
			toMain(userName) {
				this.login(userName);
				/**
				 * 强制登录时使用reLaunch方式跳转过来
				 * 返回首页也使用reLaunch方式
				 */
				if (this.forcedLogin) {
					uni.reLaunch({
						url: '../main/main',
					});
				} else {
					uni.navigateBack();
				}

			}
		},
		onReady() {
			this.initPosition();
			this.initProvider();
		}
	}
</script>

<style scoped>
	input::-webkit-input-placeholder {
		color: #fff;
	}

	input::placeholder {
		color: #fff;
	}

	.action-row {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		position: relative;
		z-index: 3;
		color: #fff;
		width: 591rpx;
		margin: 0 auto;
	}

	.action-row navigator {
		color: #fff;
		padding: 0 20upx;
	}

	.oauth-row {
		display: flex;
		flex-direction: row;
		justify-content: center;
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
	}

	.oauth-image {
		width: 100upx;
		height: 100upx;
		border: 1upx solid #dddddd;
		border-radius: 100upx;
		margin: 0 40upx;
		background-color: #ffffff;
	}

	.oauth-image image {
		width: 60upx;
		height: 60upx;
		margin: 20upx;
	}

	.loginbg {
		position: absolute;
		bottom: 0;
		width: 100%;
		height: 100vh;
	}

	.login_logo {
		position: relative;
		z-index: 3;
		display: block;

		margin: 0 auto;
		padding-top: 56rpx;
	}

	.login_logo image {
		width: 388rpx;
		height: 120rpx;
	}

	.login_type {
		width: 591rpx;
		display: flex;
		align-items: center;
		margin: 150rpx auto 20rpx;
	}

	.login_type>view {
		font-size: 30rpx;
		color: #fff;
		position: relative;
		z-index: 3;
		margin-right: 30rpx;
		opacity: 0.71;
	}

	.login_type view.cur {
		font-size: 34rpx;
		opacity: 1;
	}

	.login_type view.cur text {
		position: absolute;
		z-index: 3;
		bottom: -20rpx;
		left: 50%;
		margin-left: -6px;
		width: 11rpx;
		height: 11rpx;
		background: rgba(255, 255, 255, 1);
		border-radius: 50%;
	}

	.input-group {
		width: 591rpx;
		margin: 20rpx auto 20rpx;
		background-color: rgba(0, 0, 0, 0);
	}

	.pl_class {
		color: #fff;
	}

	.sub_btn {
		width: 591rpx;
		height: 81rpx;
		background: rgba(255, 255, 255, 1);
		border-radius: 35rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 32rpx;
		font-family: PingFang;
		font-weight: bold;
		color: rgba(61, 113, 230, 1);
		margin: 160rpx auto 0;
		position: relative;
		z-index: 3;
	}

	.input-group::before {
		display: none;
	}

	.input-row {
		display: flex;
		align-items: center;
	}

	.input-row input {
		font-size: 28rpx;
	}

	.getyzm {
		flex: none;
		display: flex;
		align-items: center;
		width: 193rpx;
		height: 59rpx;
		background: rgba(255, 255, 255, 1);
		border-radius: 29rpx;
		color: #2C55C1;
		justify-content: center;
	}

	.xieyi {
		margin: 20px auto;
		width: 591rpx;
		font-size: 20rpx;
		color: #fff;
		position: relative;
		z-index: 3;
		opacity: 0.78;
		font-size: 20rpx;
	}

	.xieyi navigator {
		display: inline;
		text-decoration: underline;
		word-wrap: break-word;
		word-break: break-all;
	}
</style>

<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">修改密码</block>
		</cu-custom>
		<image class="loginbg" src="../../static/img/loginbg_03.jpg" mode="aspectFill"></image>
		<view class="login_logo">
			<image src="../../static/img/login_logo.png" mode=""></image>
		</view>
		<view class="input-group">
			<view class="input-row  border">
				<text class="title iconfont iconshouji" style="width: 60rpx;"></text>
				<input class="m-input" type="number" clearable focus v-model="account" placeholder="请输入账号" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view class="input-row border" style="margin-top: 20rpx;">
				<text class="title iconfont iconanquan" style="width: 60rpx;"></text>
				<input type="text" v-model="code" placeholder="请输入验证码" placeholder-style="color:#fff;" placeholder-class="pl_class"></input>
				<view v-if="yzm_type==0" class="getyzm" @tap="getCode">获取验证码</view>
				<view v-if="yzm_type==1" class="getyzm">{{yztime}}s</view>
			</view>
			<view class="input-row border" style="margin-top: 20rpx;">
				<text class="title iconfont iconsuo" style="width: 60rpx;"></text>
				<input type="password" displayable v-model="password" placeholder="请输入密码" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view class="input-row border" style="margin-top: 20rpx;">
				<text class="title iconfont iconsuo" style="width: 60rpx;"></text>
				<input type="password" displayable v-model="password1" placeholder="请再次输入密码" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
		</view>

		<view class="btn-row">
			<view class="sub_btn" @tap="findPassword">提交</view>
			<!-- <button type="primary" class="primary" @tap="findPassword">提交</button> -->
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	import mInput from '../../components/m-input.vue';

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
				account: '',
				password: '',
				password1: '',
				code: '',
			}
		},
		methods: {
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
					type: 2,
					phone: that.account
				}
				service.get(jkurl, data,
					function(res) {
						that.btnkg =0
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
						that.btnkg =0
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

			findPassword() {
				var that=this
				if (that.account == '' || !(/^1\d{10}$/.test(that.account))) {
					wx.showToast({
						icon: 'none',
						title: '手机号有误'
					})
					return
				}
				if (!that.code) {
					uni.showToast({
						icon: 'none',
						title: '请输入验证码'
					});
					return;
				}
				
				if (that.password.length < 6) {
					uni.showToast({
						icon: 'none',
						title: '密码最短为 6 个字符'
					});
					return;
				}
				if (that.password!=that.password1) {
					uni.showToast({
						icon: 'none',
						title: '两次密码不一致'
					});
					return;
				}
				var data ={
					phone:that.account,
					code:that.code,
					password:that.password,
					
					affirm_password:that.password1
				}
				var jkurl='/user/forgetPwd'
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
								title: '操作成功'
							})
							// uni.setStorageSync('loginmsg', res.data.data)
							setTimeout(() => {
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

			}
		}
	}
</script>

<style>
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
		margin: 120rpx auto 20rpx;
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

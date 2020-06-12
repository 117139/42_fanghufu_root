<template>
	<view class='login' v-if="canIUse">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">授权</block>
		</cu-custom>
		<view class='header'>
			<image src='/static/img/logo.png'></image>
		</view>
		<view>医用防化服</view>
		<view class='content1'>
			<view>申请获取以下权限</view>
			<text class='huqu'>获得你的公开信息(昵称，头像等)</text>
		</view>

		<button class='bottom' type='primary' open-type="getUserInfo" lang="zh_CN" @getuserinfo="bindGetUserInfo">
			授权登录
			<!--由于微信官方修改了 getUserInfo 接口，所以现在无法实现一进入微信小程序就弹出授权窗口，只能通过 button 去触发,并且button里必须要加open-type="getUserInfo"才能弹出授权窗口-->
		</button>
		<view class='goback' @tap="goback">暂不授权</view>
	</view>
</template>

<script>
import service from '../../service.js';
	import {
		mapState,
		mapMutations
	} from 'vuex'
	export default {
		data() {
			return {
				canIUse: wx.canIUse('button.open-type.getUserInfo')
			}
		},
		computed: mapState(['forcedLogin']),
		methods: {
			...mapMutations(['login']),
			bindGetUserInfo: function(e) {
				var that =this
				if (e.detail.userInfo) {
					//用户按了允许授权按钮后需要处理的逻辑方法体
					console.log(e.detail.userInfo)
					
					wx.setStorageSync('userInfo', e.detail.userInfo)
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
												login_type:2,   ///前端自用判断是否需要跳登录
											type:3,
											wxcode:res.code,
											bdtype:1
										}
										service.post(jkurl, data,
											function(res) {
												
												if (res.data.code == 1) {
										
													uni.showToast({
														icon: 'none',
														title: '登录成功'
													})
													that.login(res.data.real_name);
													uni.setStorageSync('loginmsg', res.data.data)
													setTimeout(() => {
														uni.reLaunch({
															url: '../main/main'
														}, 1000)
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
					//用户按了拒绝按钮
					wx.showModal({
						title: '警告',
						content: '您点击了拒绝授权，将无法登录小程序，请点击返回授权!!!',
						showCancel: false,
						confirmText: '返回授权',
						success: function(res) {
							if (res.confirm) {
								console.log('用户点击了“返回授权”')
							}
						}
					})
				}
			},
			goback() {
				wx.navigateBack()
			}
		}
	}
</script>

<style scoped>
	.login {
		
		width: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 40rpx 80rpx;
		color: #333;
	}

	.header {
		width: 150rpx;
		height: 150rpx;
		margin-bottom: 20upx;
	}

	.header image {
		width: 150rpx;
		height: 150rpx;
	}

	.content1 {
		margin-top: 50rpx;
		padding: 40rpx 0;
		width: 100%;
		border-top: 1px solid #ddd;
		font-size: 30rpx;
		color: #333;
	}

	.huqu {
		font-size: 26rpx;
		color: #999;
	}

	.bottom {
		margin-top: 30rpx;
		height: 80upx;
		width: 100%;
		line-height: 80upx;
		font-size: 30upx;
	}

	.goback {
		margin-top: 30rpx;
		width: 100%;
		height: 80rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 12rpx;
		border: 1px solid #ddd;
		background: #f4f4f4;
	}
</style>

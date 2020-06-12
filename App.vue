<script>
	import Vue from 'vue'
	import service from './service.js';
	import {
		mapState,
		mapMutations
	} from 'vuex'
	export default {
		onLaunch: function() {
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
			uni.getSystemInfo({
				success: function(e) {
					// #ifndef MP
					console.log(e.statusBarHeight)
					Vue.prototype.StatusBar = e.statusBarHeight;
					if (e.platform == 'android') {
						Vue.prototype.CustomBar = e.statusBarHeight + 50;
					} else {
						Vue.prototype.CustomBar = e.statusBarHeight + 45;
					};
					// #endif
			
					// #ifdef MP-WEIXIN || MP-QQ
					Vue.prototype.StatusBar = e.statusBarHeight;
					let capsule = wx.getMenuButtonBoundingClientRect();
					if (capsule) {
						Vue.prototype.Custom = capsule;
						// Vue.prototype.capsuleSafe = uni.upx2px(750) - capsule.left + uni.upx2px(750) - capsule.right;
						Vue.prototype.CustomBar = capsule.bottom + capsule.top - e.statusBarHeight;
					} else {
						Vue.prototype.CustomBar = e.statusBarHeight + 50;
					}
					// #endif		
				
			
					// #ifdef MP-ALIPAY
					Vue.prototype.StatusBar = e.statusBarHeight;
					Vue.prototype.CustomBar = e.statusBarHeight + e.titleBarHeight;
					// #endif
				}
			})
			
			Vue.prototype.ColorList = [{
					title: '嫣红',
					name: 'red',
					color: '#e54d42'
				},
				{
					title: '桔橙',
					name: 'orange',
					color: '#f37b1d'
				},
				{
					title: '明黄',
					name: 'yellow',
					color: '#fbbd08'
				},
				{
					title: '橄榄',
					name: 'olive',
					color: '#8dc63f'
				},
				{
					title: '森绿',
					name: 'green',
					color: '#39b54a'
				},
				{
					title: '天青',
					name: 'cyan',
					color: '#1cbbb4'
				},
				{
					title: '海蓝',
					name: 'blue',
					color: '#0081ff'
				},
				{
					title: '姹紫',
					name: 'purple',
					color: '#6739b6'
				},
				{
					title: '木槿',
					name: 'mauve',
					color: '#9c26b0'
				},
				{
					title: '桃粉',
					name: 'pink',
					color: '#e03997'
				},
				{
					title: '棕褐',
					name: 'brown',
					color: '#a5673f'
				},
				{
					title: '玄灰',
					name: 'grey',
					color: '#8799a3'
				},
				{
					title: '草灰',
					name: 'gray',
					color: '#aaaaaa'
				},
				{
					title: '墨黑',
					name: 'black',
					color: '#333333'
				},
				{
					title: '雅白',
					name: 'white',
					color: '#ffffff'
				},
			]
			
		},
		onShow: function() {
			console.log('App Show');
		},
		onHide: function() {
			console.log('App Hide');
		},
		methods: {
				...mapMutations(['login','logout']),
		}
	}
</script>

<style>
	@import "colorui/main.css";
	@import "colorui/icon.css";

	/* @import "app.css"; */
	/*每个页面公共css */
	* {
		padding: 0;
	}

	page {
		min-height: 100%;
		display: flex;
	}

	/* #ifdef MP-BAIDU */
	page {
		width: 100%;
		height: 100%;
		display: block;
	}

	swan-template {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* 原生组件模式下需要注意组件外部样式 */
	custom-component {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* #endif */

	/* #ifdef MP-ALIPAY */
	page {
		min-height: 100vh;
	}

	/* #endif */

	/* 原生组件模式下需要注意组件外部样式 */
	m-input {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	.content {
		display: flex;
		flex: 1;
		flex-direction: column;
		color: #fff;
		/* background-color: #efeff4; */
		/* padding: 20upx; */
	}

	.input-group {
		background-color: #ffffff;
		margin-top: 40upx;
		position: relative;
	}

	.input-group::before {
		position: absolute;
		right: 0;
		top: 0;
		left: 0;
		height: 1upx;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.input-group::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 0;
		height: 1upx;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.input-row {
		display: flex;
		flex-direction: row;
		position: relative;
	}

	.input-row .title {
		width: 20%;
		height: 50upx;
		min-height: 50upx;
		padding: 15upx 0;
		padding-left: 30upx;
		line-height: 50upx;
	}

	.input-row.border::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 15upx;
		height: 1upx;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.btn-row {
		margin-top: 50upx;
		padding: 20upx;
	}

	button.primary {
		background-color: #0faeff;
	}

.pl_class{
			color: #fff;
		}
	@font-face {
	    font-family:"iconfont";
	    src: url('//at.alicdn.com/t/font_1851065_xlrem6nrrzd.eot?t=1591941449329');
	    /* IE9 */
	    src: url('//at.alicdn.com/t/font_1851065_xlrem6nrrzd.eot?t=1591941449329#iefix') format('embedded-opentype'),
	    /* IE6-IE8 */
	    url('data:application/x-font-woff2;charset=utf-8;base64,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') format('woff2'), url('//at.alicdn.com/t/font_1851065_xlrem6nrrzd.woff?t=1591941449329') format('woff'), url('//at.alicdn.com/t/font_1851065_xlrem6nrrzd.ttf?t=1591941449329') format('truetype'),
	    /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
	    url('//at.alicdn.com/t/font_1851065_xlrem6nrrzd.svg?t=1591941449329#iconfont') format('svg');
	    /* iOS 4.1- */
	}
	.iconfont {
	    font-family:"iconfont" !important;
	    font-size: 16px;
	    font-style: normal;
	    -webkit-font-smoothing: antialiased;
	    -moz-osx-font-smoothing: grayscale;
	}
	.iconsou_suo:before {
	    content:"\e624";
	}
	.iconxuanzhong:before {
	    content:"\e602";
	}
	.iconren:before {
	    content:"\e623";
	}
	.icondanwei:before {
	    content:"\e659";
	}
	.iconfanhui:before {
	    content:"\e600";
	}
	.iconbianji:before {
	    content:"\e644";
	}
	.iconanquan:before {
	    content:"\e69e";
	}
	.iconshouji:before {
	    content:"\e671";
	}
	.iconsuo:before {
	    content:"\e649";
	}
	.iconnext3:before {
	    content:"\e601";
	}
	.iconcaozuo:before {
	    content:"\e641";
	}


	
	.zanwu{
		width: 100%;
		text-align: center;
		font-size: 30upx;
		line-height: 100upx;
		color: #999;
	}
	.mt20{
		margin-top: 20rpx;
	}
</style>

<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">注册</block>
		</cu-custom>
		<image class="loginbg" src="../../static/img/loginbg_03.jpg" mode="aspectFill"></image>
		<view class="login_logo">
			<view class="tx_box" @tap="uptx" data-url="../main_edit/main_edit">
				<image v-if="!tximg" class="tx_img" src="../../static/img/images/zhuce_03.jpg" mode="aspectFill"></image>
				<image v-if="tximg" class="tx_img" :src="imgurl+tximg" mode="aspectFill"></image>
				<view class="tx_edit"><text class="iconfont iconbianji"></text></view>
			</view>
		</view>
		<view class="input-group">
			<view class="input-row  border" @blur="show_type=false">
				<text class="title iconfont icondanwei" style="width: 60rpx;"></text>
				<view class="dw_val" @tap="show_type=true">{{unit?unit.title:'请选择单位'}}</view>
				<text class="iconfont iconfanhui"></text>
				
				 <view  v-if="show_type"  class="dw_list">
					 <view class="dw_list_tit">
						 <view class="dw_search_box">
							 <text class="iconfont iconsou_suo"></text>
							 <input class="dw_input" type="text" clearable focus    @input='search_dw'  placeholder="请输入单位" placeholder-style="color:#fff;"
							  placeholder-class="pl_class"></input>
						 </view>
						 <view class="dw_gb" @tap="show_type=false">关闭</view>
					 </view>
					 <scroll-view scroll-y class="dw_list_box">
							<view v-if="dw_show.length==0" class="zanwu">暂无数据</view>
							<view class="dw_li" v-for="(item,index) in dw_show" v-html="item.title" @tap='danwei_get(item)'></view>
					 </scroll-view>
				 </view>
			</view>
			
			<!-- <picker @change="bindPickerChange" :value="index" :range="danwei" range-key="title">

				<view class="input-row  border">
					<text class="title iconfont icondanwei" style="width: 60rpx;"></text>
					<view class="dw_val">{{danwei[index].title}}</view>
					<text class="iconfont iconfanhui"></text>
				</view>
			</picker> -->
			<view class="input-row  border">
				<text class="title iconfont iconren" style="width: 60rpx;"></text>
				<input class="m-input" type="text" clearable v-model="uname" placeholder="请输入姓名" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view class="input-row  border">
				<text class="title iconfont iconshouji" style="width: 60rpx;"></text>
				<input class="m-input" type="number" clearable  v-model="account" placeholder="请输入手机" placeholder-style="color:#fff;"
				 placeholder-class="pl_class"></input>
			</view>
			<view class="input-row border" style="margin-top: 20rpx;">
				<text class="title iconfont iconanquan" style="width: 60rpx;"></text>
				<input type="number" v-model="code" placeholder="请输入验证码" placeholder-style="color:#fff;" placeholder-class="pl_class"></input>
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
			<view class="sub_btn" @tap="bindLogin">注册</view>
			<!-- <button type="primary" class="primary" @tap="findPassword">提交</button> -->
		</view>
		<!-- <checkbox-group @change="checkboxChange">
			<label class="xieyi">
				<checkbox value="cb" style="transform:scale(0.5)" />同意
				<navigator url="../xieyi/xieyi">《北京邦维高科特种纺织品有限责任公司许可协议》</navigator>
			</label>
		</checkbox-group> -->
		<view  class="xieyi">
			<text class="iconfont iconxuanzhong" :class="{'cur':xieyi_type}" @tap="xieyi_type=!xieyi_type"></text>
			<text @tap="xieyi_type=!xieyi_type">同意</text>
			<navigator url="../xieyi/xieyi">《北京邦维高科特种纺织品有限责任公司许可协议》</navigator>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	import mInput from '../../components/m-input.vue';
	import {
		mapState,
		mapMutations
	} from 'vuex'
	var inputt
	export default {
		components: {
			mInput
		},
		data() {
			return {
				imgurl:'',
				btnkg: 0,
				logintype: '0',
				danwei:[
					{
							"id": 1,
							"title": "博雅医院",
							"img": ""
					},
					{
							"id": 2,
							"title": "五四一医院",
							"img": ""
					},
					{
							"id": 3,
							"title": "北京以员123",
							"img": ""
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					}
				],
				dw_show:[
					{
							"id": 1,
							"title": "博雅医院",
							"img": ""
					},
					{
							"id": 2,
							"title": "五四一医院",
							"img": ""
					},
					{
							"id": 3,
							"title": "北京以员123",
							"img": ""
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					},
					{
							"id": 4,
							"title": "123123456456",
							"img": "/storage/uploads/admin/category/img/5eda07de09ea5.jpg"
					}
				],
				unit:'',
				unit_id:'',
				show_type:'',
				yzm_type: 0,
				yztime: 60,
				tximg: '/static/cms/img/user_logo.png',
				uname: '',
				account: '',
				password: '',
				password1: '',
				code: '',

				array: ['中国', '美国', '巴西', '日本'],
				index: 0,
				xieyi_type: false
			}
		},
		onLoad() {
			this.getdw()
			this.imgurl=service.imgurl
		},
		computed: mapState(['forcedLogin']),
		methods: {
			...mapMutations(['login']),
			uptx() {
				var that = this
				// 从相册选择6张图
				uni.chooseImage({
					count: 1,
					sizeType: ['original', 'compressed'],
					sourceType: ['album'],
					success: function(res) {
						console.log(res)
						var tximg = res.tempFilePaths[0]
						uni.uploadFile({
							url: service.IPurl+'/upload/streamImg', //仅为示例，非真实的接口地址
							filePath:tximg,
							name: 'file',
							formData: {
								type: 1
							},
							success: (uploadFileRes) => {
								console.log(uploadFileRes.data);
								var ndata = JSON.parse(uploadFileRes.data)
								if(ndata.code==1){
									that.tximg=ndata.msg
								}
							}
						});
					}
				});
			},
			search_dw(e){
				var that=this
				console.log(e.detail.value)
				clearInterval(inputt)
						inputt = setTimeout(function () {
								var kw = e.detail.value
								console.log(kw.length)
								if (kw.length > 0){
									var news=[]
									for(var i=0;i<that.danwei.length;i++){
										var str=that.danwei[i].title
										if(str.indexOf(kw) != -1){
											news.push(that.danwei[i])
										}
									}
									that.dw_show=news
								}else{
									that.dw_show=that.danwei
								}
						},400)
			},
			danwei_get(str){
				var that=this
				that.unit=str
				that.show_type=false
			},
			getdw(){
				var that =this
				///cate/list
				var jkurl="/cate/list"
				var data={
					type:1
				}
				service.get(jkurl, data,
					function(res) {
						
						if (res.data.code == 1) {
							console.log(res.data.data)
							that.danwei=res.data.data
							that.dw_show= JSON.parse(JSON.stringify(res.data.data))
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
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.target.value)
				this.index = e.target.value
			},
			checkboxChange: function(e) {
				var that = this
				console.log('picker发送选择改变，携带值为', e.target.value)
				var arr = e.target.value
				if (arr.length > 0) {
					that.xieyi_type = true
				} else {
					that.xieyi_type = false
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
					type: 1,
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
				if (!that.xieyi_type) {
					uni.showToast({
						icon: 'none',
						title: '请先阅读协议'
					});
					return
				}
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
									var userInfo = res.userInfo
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
								url: '../login0/login0'
							})
						}
					}
				})
				return
				// #endif

				that.m_login()


			},
			m_login(rescode) {
				var that = this

				if (!that.tximg) {
					uni.showToast({
						icon: 'none',
						title: '请上传头像'
					});
					return;
				}
				if (!that.uname) {
					uni.showToast({
						icon: 'none',
						title: '请输入姓名'
					});
					return;
				}
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
				if (that.password != that.password1) {
					uni.showToast({
						icon: 'none',
						title: '两次密码不一致'
					});
					return;
				}
				var data = {
					avatarurl:that.tximg,
					unit:that.danwei[that.index].id,
					real_name:that.uname,
					phone: that.account,
					code: that.code,
					password: that.password,
					affirm_password: that.password1,

					affirm_password: that.password1,
					type:1
				}
				// #ifdef MP-WEIXIN
				data = {
					avatarurl:that.tximg,
					unit:that.danwei[that.index].id,
					real_name:that.uname,
					phone: that.account,
					code: that.code,
					password: that.password,
					affirm_password: that.password1,

					affirm_password: that.password1,
					type:2,
					wxcode:rescode
				}
				// #endif
				var jkurl = '/register'
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
								title: '操作成功'
							})
							that.login(res.data.real_name);
							uni.setStorageSync('loginmsg', res.data.data)
							setTimeout(() => {
								uni.reLaunch({
									url:'../main/main'
								})
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
	.dw_list{
		width: 100%;
		height: 190px;
		background: #fff;
		position: absolute;
		top: 80upx;
		z-index: 99;
	}
	.dw_list_tit{
		width: 100%;
		height: 40px;
		padding: 5px;
		-webkit-box-sizing: border-box;
		-moz-box-sizing: border-box;
		box-sizing: border-box;
		color: #000;
		border-bottom: 1px solid #ddd;
		display: flex;
	}
	.dw_gb{
		width: 30px;
		display: flex;
		align-items: center;
		justify-content: center;
		height: 30px;
	}
	.dw_search_box{
		flex: 1;
		border: 1px solid #eee;
		height: 30px;
		display: flex;
		align-items: center;
		padding: 0 10px;
	}
	.iconsou_suo{
		font-size: 18px;
		width: 30px;
	}
	.dw_input{
		flex: 1;
		min-width: 0;
	}
	.dw_list_box{
		height: 150px;
	}
	.dw_li{
		height: 34px;
		line-height: 34px;
		text-align: center;
		color: #000;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
		border-bottom: 1px solid #eee;
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
		z-index: 9;
	}

	.login_logo {
		position: relative;
		z-index: 3;
		display: block;

		margin: 0 auto;
		padding-top: 140rpx;
	}



	.login_type {
		width: 591rpx;
		display: flex;
		align-items: center;
		margin: 20rpx auto 20rpx;
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
		margin: 70rpx auto 20rpx;
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
		margin: 40rpx auto 0;
		position: relative;
		z-index: 3;
	}

	.input-group::before {
		display: none;
	}

	.input-row {
		display: flex;
		align-items: center;
		position: relative;
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

	.dw_val {
		flex: 1;
	}

	checkbox-group {
		margin: 20px auto;
		width: 591rpx;
	}

	.xieyi {
		margin: 15upx auto;
		width: 591rpx;
		font-size: 20rpx;
		color: #fff;
		position: relative;
		z-index: 3;
		opacity: 0.78;
		font-size: 20rpx;
		text-align: center;
		display: flex;
		flex-wrap: wrap;
		align-items: center;
	}

	.xieyi navigator {
		display: inline;
		text-decoration: underline;
		word-wrap: break-word;
		word-break: break-all;
	}
	.xieyi .iconfont{
		border: 1upx solid #ddd;
		color: rgba(0,0,0,0);
		border-radius: 50%;
		margin-right: 10rpx;
		padding: 0;
		font-size: 24upx;
		line-height: 24upx;
		-webkit-box-sizing: border-box;
		-moz-box-sizing: border-box;
		box-sizing: border-box;
	}
	.xieyi .cur{
		font-size: 26upx;
		line-height: 26upx;
		border: 0;
		color: yellow;
	}
</style>

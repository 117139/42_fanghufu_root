<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">个人信息</block>
		</cu-custom>
		<image class="index_bg" src="../../static/img/images/index1bg_01.jpg"></image>
		<view v-if="hasLogin" class="index_box">
			<view class="hello">

				<view class="title">
					<view class="tx_box" @tap="uptx" data-url="../main_edit1/main_edit1">
						<image v-if="!tximg" :src="imgurl+loginmsg.avatarurl"></image>
						<image v-if="tximg" :src="imgurl+tximg"></image>
						<view class="tx_edit"><text class="iconfont iconbianji"></text></view>
					</view>
					<view class="uname">{{loginmsg.real_name}}</view>
				</view>

			</view>

		</view>
		<view v-if="!hasLogin" class="index_box">
			<view class="hello">
		
				<view class="title">
					<view class="tx_box"  @tap="bindLogin">
						<image :src="imgurl+'/static/cms/img/user_logo.png'"></image>
						<view class="tx_edit"><text class="iconfont iconbianji"></text></view>
					</view>
					<view class="uname"> 您好 游客</view>
					<button v-if="!hasLogin" type="primary" class="primary login_btn" @tap="bindLogin">登录</button>
				</view>
		
			</view>
			
		</view>
		<picker  v-if="hasLogin" @change="bindPickerChange" :value="index" :range="array" range-key="title">

			<view class="cz_li mt20">
				<view class="cz_tit">单位</view>
				<view class="dw_val">{{danwei}}</view>
				<text class="iconfont iconfanhui"></text>
			</view>
		</picker>
		<view  v-if="hasLogin" class="cz_li">
			<view class="cz_tit">姓名</view>
			<input type="text" v-model="uname" placeholder="请输入" />
		</view>
		<view  v-if="hasLogin" class="cz_li">
			<view class="cz_tit">电话</view>
			<input style="color: #999;" type="number" v-model="tel" placeholder="请输入" disabled="" />
		</view>
		<view  v-if="hasLogin" class="cz_v">
			<view class="cz_btn" @tap="upInfo">保存</view>
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
				imgurl: '',
				top: '50',
				loginmsg: '',
				array: ['中国', '美国', '巴西', '日本'],
				index: -1,
				tximg: '',
				danwei: '',
				uname: '',
				tel: '',
			}
		},
		onLoad() {
			var that = this
			that.imgurl = service.imgurl
			that.getdw()
			that.loginmsg = uni.getStorageSync('loginmsg')
			that.tximg = uni.getStorageSync('loginmsg').avatarurl
			that.danwei = uni.getStorageSync('loginmsg').unit
			that.uname = uni.getStorageSync('loginmsg').real_name
			that.tel = uni.getStorageSync('loginmsg').phone
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
		methods: {
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
							url: service.IPurl + '/upload/streamImg', //仅为示例，非真实的接口地址
							filePath: tximg,
							name: 'file',
							formData: {
								type: 1
							},
							success: (uploadFileRes) => {
								console.log(uploadFileRes.data);
								var ndata = JSON.parse(uploadFileRes.data)
								if (ndata.code == 1) {
									that.tximg = ndata.msg
								}
							}
						});
					}
				});
			},
			upInfo() {
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
				// if (that.account == '' || !(/^1\d{10}$/.test(that.account))) {
				// 	wx.showToast({
				// 		icon: 'none',
				// 		title: '手机号有误'
				// 	})
				// 	return
				// }
				// if (!that.code) {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '请输入验证码'
				// 	});
				// 	return;
				// }
				var data = {
					token: uni.getStorageSync('loginmsg').userToken,
					avatarurl: that.tximg,
					real_name: that.uname,
					// phone: that.account,
				}
				if (that.index == -1) {
					data = {
						token: uni.getStorageSync('loginmsg').userToken,
						avatarurl: that.tximg,
						real_name: that.uname,
						// phone: that.account,
					}
				}else{
					data = {
						token: uni.getStorageSync('loginmsg').userToken,
						avatarurl: that.tximg,
						unit: that.array[that.index].id,
						real_name: that.uname,
						// phone: that.account,
					}
				}


				var jkurl = '/user/upInfo'
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
							setTimeout(function (){
								that.m_login()
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
			m_login() {
				var that = this
				var jkurl = '/user/info'
				var data = {
					token: uni.getStorageSync('loginmsg').userToken,
				}
				service.get(jkurl, data,
					function(res) {

						if (res.data.code == 1) {


							uni.setStorageSync('loginmsg', res.data.data)
							that.loginmsg = res.data.data
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
			},

			getdw() {
				var that = this
				///cate/list
				var jkurl = "/cate/list"
				var data = {
					type: 1
				}
				service.get(jkurl, data,
					function(res) {

						if (res.data.code == 1) {
							console.log(res.data.data)
							that.array = res.data.data
							var newsarr=res.data.data
							for (var i =0;i<newsarr.length;i++) {
								if(that.danwei==newsarr[i].title){
									that.index=i
								}
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

			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.target.value)
				this.index = e.target.value
				this.danwei = this.array[this.index].title
			},
			bindLogin() {
				uni.navigateTo({
					url: '../login/login',
				});
			},
			upimg() {

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

	.cu-bar .action {
		color: #fff;
	}

	.hello {
		padding-top: 50upx;
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
		margin-top: 20upx;
		text-align: center;
	}

	.uname1 {
		font-size: 28upx;
		font-family: PingFang SC;
		font-weight: 500;
		color: rgba(254, 254, 254, 1);
		margin-top: 20upx;
		text-align: center;
	}
	.login_btn {
		width: 150upx;
		height: 70upx;
		line-height: 70upx;
		font-size: 34upx;
		margin-top: 20upx;
	}
	.cz_li {
		width: 100%;
		height: 100rpx;

		padding: 0 28rpx;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 28rpx;
		color: #333;
		background: #FFFFFF;
		border-bottom: 1px solid #f7f7f7;
	}

	.cz_v {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-top: 200rpx;
	}

	.cz_btn {
		width: 570rpx;
		height: 78rpx;
		background: linear-gradient(-55deg, rgba(81, 95, 235, 1), rgba(63, 129, 253, 1));
		border-radius: 39rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.cz_tit {
		font-size: 30rpx;
		color: #333;
	}

	.cz_li input {
		flex: 1;
		text-align: right;
		font-size: 30rpx;
		color: #333;
	}

	.dw_val {
		flex: 1;
		text-align: right;
	}

	.cz_li text {
		margin-left: 10rpx;
		color: #999;
	}
</style>

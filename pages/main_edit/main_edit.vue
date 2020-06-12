<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true"><block slot="backText">返回</block><block slot="content">个人信息</block></cu-custom>
		<image class="index_bg" src="../../static/img/images/index1bg_01.jpg"></image>
		<view v-if="hasLogin" class="index_box">
			<view class="hello">

				<view class="title">
					<view class="tx_box" @tap="jump" data-url="../main_edit1/main_edit1">
						<image :src="imgurl+loginmsg.avatarurl"></image>
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
		<view v-if="hasLogin" class="cz_li mt20">
			<view>版本号</view>
			<view>V1.0</view>
		</view>
		<!-- #ifdef APP-PLUS -->
		<view v-if="hasLogin" class="cz_v">
			<view class="cz_btn" @tap="logout_fuc">退出登录</view>
		</view>
		<!-- #endif -->
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
				btnkg:0,
				imgurl:'',
				top: '50',
				loginmsg:''
			}
		},
		onLoad() {
			var that = this
			that.loginmsg=uni.getStorageSync('loginmsg')
			that.imgurl=service.imgurl
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
			...mapMutations(['logout']),
			bindLogin() {
				uni.navigateTo({
					url: '../login/login',
				});
			},

			jump(e) {
				var that =this
				if(that.btnkg==1){
					return
				}else{
					that.btnkg=1
					setTimeout(function(){
						that.btnkg=0
					},1000)
				}
				console.log(e.currentTarget.dataset.url)
				uni.navigateTo({
					url: e.currentTarget.dataset.url,
				});
			},
			logout_fuc(){
				var that =this
				uni.showModal({
				    title: '提示',
				    content: '是否要退出登录',
				    success: function (res) {
				        if (res.confirm) {
				            // console.log('用户点击确定');
										uni.removeStorageSync('userInfo')
										uni.removeStorageSync('loginmsg')
										uni.setStorageSync('phone', '')
										that.logout()
										setTimeout(function(){
											uni.reLaunch({
											    url: '../main/main'
											});
										})
				        } else if (res.cancel) {
				            console.log('用户点击取消');
				        }
				    }
				});
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
</style>

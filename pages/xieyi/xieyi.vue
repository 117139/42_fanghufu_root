<template>
	<view class="content">
		<cu-custom bgColor="#4663ed" :isBack="true">
		    <block slot="backText" >返回</block>
		    <!-- <block slot="content">协议</block> -->
		</cu-custom>
		<view class="xieyi_box" v-html='xieyi'>
			协议协议协议协议协议协议协议协议协议协议协议协议协议协议协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			协议
			
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
				xieyi: '',
			}
		},
		onLoad() {
			var that = this

			that.getxieyi()
		},
		methods: {
			bindLogin() {
				uni.navigateTo({
					url: '../login/login',
				});
			},

			getxieyi(e) {
				var that =this
				///agreement
				var jkurl='/agreement'
				var data={}
				service.get(jkurl, data,
					function(res) {
				
						if (res.data.code == 1) {
							console.log(res.data.data)
							that.xieyi = res.data.data.content
							
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
		padding-top: 200upx;
		display: flex;
		/* flex: 1; */
		flex-direction: column;
		align-items: center;
		height: 541upx;
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
		color: #333;
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
		margin-top: 50upx;
	}

	.uname1 {
		font-size: 28upx;
		font-family: PingFang SC;
		font-weight: 500;
		color: rgba(254, 254, 254, 1)
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
	.xieyi_box{
		width: 100%;
		padding: 0 30rpx;
		box-sizing: border-box;
	}
</style>


<template>
    <div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar" alt="" width="64" height="64">
			</div>
			<div class="content">
				<div class="title">
					<span class="brand">品牌</span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">
					{{seller.description}}/{{seller.deliveryTime}}分钟送达
				</div>
				<div class="support" v-if="seller.supports">
					<span class="icon"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
		</div>
		<div class="bulletin-wrapper"></div>
    </div>
</template>

<script>
export default {
	props: {
		seller: {
			type: Object
		}
	}
}
</script>

<style lang="less">
 @import '../../common/less/base';
.header{
	background-color: #000;
	color: #fff;
	.content-wrapper{
		padding: 24px 12px 18px 24px;
		font-size: 0;	/*并列元素有换行空格产生的空隙，父元素fz=0，子元素要设fz*/
		.avatar{
			display: inline-block;
		}
		.content{
			display: inline-block;
			font-size: 14px;
			margin-left: 16px;
			.title{
				margin: 2px 0 8px 0;
				.brand{
					width: 30px;
					height: 18px;
					display: inline-block;
					background-color: orange;
					text-align: center;
					line-height: 18px;
				}
				.name{
					margin-left: 6px;
					font-size: 16px;
					font-weight: bold;
				}
			}
		}
	}
}
</style>
<template>
	<div class="goods">
		<div class="menu-wrapper" v-el:menu-wrapper>
			<ul>
				<li v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}">
					<span class="text border-1px">
						<span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
					{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" v-el:foods-wrapper>
			<ul>
				<li v-for="item in goods" class="food-list food-list-hook">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li v-for="food in item.foods" class="food-item border-1px">
							<div class="icon">
								<img width="57" height="57" :src="food.icon" alt="">
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span class="count">月售{{food.sellCount}}份</span>
									<span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">￥{{food.price}}</span>
									<span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
const ERR_OK = 0;
export default {
	props: {
		seller: {
			type: Object
		}
	},
	data() {
		return {
			goods: [],
			listHeight: [],
			scrollY: 0
		};
	},
	computed: {
		currentIndex() {
			for (let i = 0; i < this.listHeight.length; i++) {
				let height1 = this.listHeight[i];
				let height2 = this.listHeight[i + 1];
				if (!height2 || (this.scrollY > height1 && this.scrollY < height2)) {
					return i;
				}
			}
			return 0;
		}
	},
	created() {
        this.classMap = ['descrase', 'discount', 'special', 'invoice', 'guarantee'];
		this.$http.get('/api/goods').then((response) => {
			response = response.body;
			if (response.errno === ERR_OK) {
				this.goods = response.data;
				this.$nextTick(() => {
					this._initScroll();
					this._calculateHeight();
				});
			}
		});
	},
	methods: {
		_initScroll() {
			this.meunScroll = new BScroll(this.$els.menuWrapper, { });
			this.foodScroll = new BScroll(this.$els.foodsWrapper, {
				probeType: 3
			});

			this.foodsScroll.on('scroll', (pos) => {
				this.scrollY = Math.abs(Math.round(pos.y));
			});
		},
		_calculateHeight() {
			let foodList = this.$els.foodsWrapper.getElementByClassName('food-list-hook');
			let height = 0;
			this.listHeight.push(height);
			for (let i = 0; i < foodList.length; i++) {
				let item = foodList[i];
				height += item.clientHeight;
				this.listHeight.push(height);
			}
		}
	}
};
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"

.goods
	display:flex
	position:absolute
	top:174px
	width:100%
	bottom:46px
	overflow:hidden
	.menu-wrapper
		flex:0 0 80px
		width:80px
		background:#f3f5f7
		.menu-item
			display:table
			height:54px
			width:56px
			line-height:14px
			padding:0 12px
			&.current
				position:relative
				margin-top:-1px
				z-index:10
				background:#fff
				font-weight:700
				.text
					border-none()
			.text
				display:table-cell
				width:56px
				vertical-align:middle
				font-size:12px
				border-1px(rgba(7,17,27,0.1))
			.icon
				display:inline-block
				vertical-align:top
				width:12px
				height:12px
				margin-right:4px
				background-size:12px 12px
				background-repeat:no-repeat
				&.descrase
					bg-image('decrease_1')
				&.discount
					bg-image('discount_1')
				&.guarantee
					bg-image('guarantee_1')
				&.special
					bg-image('special_1')
				&.invoice
					bg-image('invoice_1')
	.foods-wrapper
		flex:1
		.title
			padding-left:14px
			height:26px
			line-height:26px
			border-left:2px solid #d9dde1
			font-size:12px
			color:rgb(147,153,159)
			background:#f3f5f7
		.food-item
			display:flex
			margin:18px
			padding-bottom:18px
			border-1px(rgba(7,7,27,0.1))
			&:last-child
				border-none()
				margin-bottom:0
			.icon
				flex:0 0 57px
				margin-right:10px
			.content
				flex:1
				.name
					margin:2px 0 8px 0
					height:14px
					line-height:14px
					font-size:14px
					color:rgb(7,17,27)
				.desc,.extra
					line-height:10px
					font-size:10px
					color:rgb(147,153,159)
				.desc
					margin-bottom:8px
				.extra
					.count
						margin-right:12px
				.price
					font-weight:700
					line-height:24px
					.now
						margin-right:8px
						font-size:14px
						color:rgb(240,20,20)
					.old
						text-decoration:line-through
						font-size:10px
						color:rgb(147,153,159)




</style>

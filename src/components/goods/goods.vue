<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods"  :class="index==currentIndex?'menu-item-select':'menu-item'" @click="menuClick(index,$event)">
          <span class="text border-1px"><span v-show="item.type > 0" :class="classMap[item.type] " class="icon"></span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food,$event)">
              <div class="icon">
                <img :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="description">{{food.description}}</p>
                <div class="extra">
                  <span class="sellCount">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}</span>
                </div>
                <div class="price">
                  <span>￥<span class="price-number">{{food.price}}</span></span>
                  <span v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cart-wrapper">
                  <cartcontrol :food="food" ></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :delivery-price="seller.deliveryPrice" :selectFoods="selectFoods"
              :min-price = "seller.minPrice" ref="shopcart"></shopcart>
    <food :food="selectedfood" ref="myfood"></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-Scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import food from 'components/food/food';

  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      'shopcart': shopcart,
      'cartcontrol': cartcontrol,
      'food': food
    },
    data () {
      return {
        goods: [],
        foodsHeight: [],
        scrollY: 0,
        selectedfood: {}
      };
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.foodsHeight.length; i++) {
          let startHeight = this.foodsHeight[i];
          let endHeight = this.foodsHeight[i + 1];
          if (!endHeight || (this.scrollY >= startHeight && this.scrollY < endHeight)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'guarantee', 'invoice'];

      this.$http.get('/api/goods').then(response => {
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
      selectFood (food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectedfood = food;
        this.$refs.myfood.showToggle();
      },
      _initScroll () {
        this.menuscroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight () {
        let foodsList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.foodsHeight.push(height);
        for (let i = 0; i < foodsList.length; i++) {
          let item = foodsList[i];
          height += item.clientHeight;
          this.foodsHeight.push(height);
        }
      },
      menuClick (index, event) {
        if (!event._constructed) {
          return;
        }
        let foodsList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodsList[index];
        this.foodsScroll.scrollToElement(el, 300);
      }
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .goods
    display flex
    width 100%
    position absolute
    top 174px
    left 0
    bottom 48px
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      .menu-item,.menu-item-select
        display table
        height 54px
        width 56px
        line-height 14px
        padding 0 12px
        &.menu-item-select
          position relative
          margin-top -1px
          background-color #ffffff
          z-index 50
          font-weight 700
          .text
            border-none()
        .text
          display table-cell
          vertical-align middle
          color rgb(77, 85, 93)
          border-1px(rgba(7, 17, 27, 0.1))
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 2px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_3')
            &.discount
              bg-image('discount_3')
            &.guarantee
              bg-image('guarantee_3')
            &.invoice
              bg-image('invoice_3')
            &.special
              bg-image('special_3')

    .foods-wrapper
      flex 1
      .title
        height 26px
        background #f3f5f7
        font-size 12px
        font-weight 500
        line-height 26px
        color rgb(147, 153, 159)
        border-left 2px solid #d9ddd1
        padding-left 10px
      .food-item
        position relative
        display flex
        margin 0 18px
        padding 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .icon
          & >img
            flex 0 0 57px
            width 57px
            margin-right 10px
        .content
          flex 1
          & > h2
            font-size 14px
            line-height 14px
            color rgb(7, 17, 27)
            margin 2px 0 8px 0
            font-weight 700
          .description
            font-size 10px
            line-height 10px
            margin-bottom 8px
            color rgb(147, 153, 159)
        .extra
          font-size 10px
          line-height 10px
          color rgb(147, 153, 159)
          .sellCount
            margin-right 12px
        .price
          font-size 10px
          color rgb(240, 20, 20)
          line-height 24px
          .price-number
            font-size 14px
            font-weight 700
          .oldPrice
            font-size 10px
            color rgb(147, 153, 159)
            font-weight 700
            text-decoration line-through
        .cart-wrapper
          position absolute
          right 0
          bottom 12px
</style>

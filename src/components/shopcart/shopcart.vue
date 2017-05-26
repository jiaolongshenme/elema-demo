
<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left" @click="listToggle">
        <div class="icon-wrapper">
          <div class="icon" :class="{'active':totalPrice}">
            <i class="icon-shopping_cart"></i>
          </div>
          <div v-show="totalCount" class="badge">{{totalCount}}</div>
        </div>
      <div class="price">¥{{totalPrice}}</div>
      <div class="desc">另需配送费¥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" :class="{'active':totalPrice>=minPrice}">
        {{payDesc}}
      </div>
      <div class="ball-container">
        <transition name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter"
                    v-for="(ball, index) in balls">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook"></div>
          </div>
        </transition>
      </div>
    </div>
    <transition name="transHeight">
      <div class="shopcart-list" v-show="ListShow">
        <div class="list-header">
          <h2 class="title">购物车</h2>
          <span class="empty" @click="emptyList">清空</span>
        </div>
        <div class="list-content" ref="foodList">
          <ul>
            <li class="food border-1px" v-for="food in selectFoods" >
              <span class="name">{{food.name}}</span>
              <div class="price">
                ¥<span>{{food.price*food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="fade">
      <div class="backdrop" v-show="backdrop" @click="hideDrop"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import BScroll from 'better-Scroll';

  export default {
    props: {
      selectFoods: {
        type: Array,
        default () {
          return [];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data () {
      return {
        ListShow: false,
        balls: [
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ],
        dropBalls: []
      };
    },
    computed: {
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach((food) => {
          if (food.count) {
            total += food.count * food.price;
          }
        });
        return total;
      },
      totalCount () {
        let count = 0;
        this.selectFoods.forEach((food) => {
          if (food.count) {
            count += food.count;
          }
        });
        return count;
      },
      payDesc () {
        let dif = this.minPrice - this.totalPrice;
        if (!this.totalPrice) {
          return `¥${this.minPrice}起送`;
        } else if (this.totalPrice < this.minPrice) {
          return `还差¥${dif}起送`;
        } else {
          return '去下单';
        }
      },
      backdrop () {
        if (this.ListShow && this.totalPrice) {
          return true;
        }
        this.ListShow = false;
        return false;
      }
    },
    created () {
      this.$root.eventHub.$on('cart.add', this.drop);
    },
    methods: {
      listToggle () {
        if (!this.selectFoods.length) {
          return;
        }
        this.ListShow = !this.ListShow;
        if (this.ListShow) {
          this.$nextTick(() => {
            if (!this.listScroll) {
              this._initScroll();
            } else {
              this.listScroll.refresh();
            }
          });
        }
      },
      hideDrop () {
        this.ListShow = false;
      },
      emptyList () {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      _initScroll () {
        this.listScroll = new BScroll(this.$refs.foodList, {
          click: true
        });
      },
      drop (el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      beforeEnter (el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
            el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
            el.style.transform = `translate3d(0, ${y}px, 0)`;
            let inner = el.querySelector('.inner-hook');
            inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
            inner.style.transform = `translate3d(${x}px, 0, 0)`;
          }
        }
      },
      enter (el) {
        el.offsetHeight; // 触发浏览器重绘
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0, 0, 0)';
          el.style.transform = 'translate3d(0, 0, 0)';
          let inner = el.querySelector('.inner-hook');
          inner.style.webkitTransform = 'translate3d(0, 0, 0)';
          inner.style.transform = 'translate3d(0, 0, 0)';
        });
      },
      afterEnter (el) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        }
      }
    },
    components: {
      'cartcontrol': cartcontrol
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .shopcart
    position fixed
    left 0
    bottom 0
    height 48px
    width 100%
    z-index 50
    .content
      display flex
      background #141d27
      .content-left
        flex 1
        .icon-wrapper
          display inline-block
          position relative
          z-index 100
          top -10px
          left 12px
          width 56px
          height 56px
          border-radius 56px
          background-color #141d27
          .badge
            position absolute
            top 0
            left 26px
            font-size 12px
            line-height 16px
            padding 0 6px
            color #fff
            background-color rgb(240, 20, 20)
            box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
            border-radius 18px
          .icon
            width 44px
            height 44px
            border-radius 44px
            margin 6px
            z-index 200
            background-color #2b343c
            text-align center
            .icon-shopping_cart
              font-size 24px
              color rgba(255, 255, 255, 0.4)
              line-height 44px
            &.active
              background-color rgb(0,160,220)
              .icon-shopping_cart
                color #fff
        .price
          display inline-block
          vertical-align top
          margin 12px 12px 12px 18px
          padding-right 12px
          font-size 16px
          font-weight 700
          color rgba(255, 255, 255, 0.4)
          line-height 24px
          border-right 1px solid rgba(255, 255, 255, 0.1)
        .desc
          display inline-block
          vertical-align top
          font-size 12px
          color rgba(255, 255, 255, 0.4)
          font-weight 200
          line-height 48px
      .content-right
        flex 0 0 105px
        width 105px
        background-color #2b343c
        font-size 14px
        line-height 48px
        font-weight 700
        text-align center
        color rgba(255, 255, 255, 0.4)
        &.active
          background-color #00b43c
          color #fff
    .shopcart-list
      position absolute
      left 0
      top 0
      width 100%
      background #fff
      z-index -1
      transform translate3d(0,-100%,0)
      &.transHeight-enter-active,&.transHeight-leave-active
        transition all 0.5s
      &.transHeight-enter,&.transHeight-leave-active
        transform translate3d(0,0,0)
      .list-header
        height 40px
        width 100%
        background #f3f5f7
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        line-height 40px
        .title
          display inline-block
          margin-left 18px
          font-size 14px
          font-weight 200
          color rgb(7, 17, 27)
        .empty
          position absolute
          right 8px
          padding 0 10px
          font-size 12px
          color rgb(0, 160, 220)
      .list-content
        font-size 0
        max-height 217px
        overflow hidden
        .food
          position relative
          margin 0 18px
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            font-size 14px
            color rgb(7, 17, 27)
            line-height 48px
            font-weight 700
          .price
            position absolute
            right 90px
            bottom 0
            font-size 10px
            line-height 48px
            color rgb(240, 20, 20)
            padding 0 12px 0 18px
            & > span
              font-size 14px
              font-weight 700
              line-height 48px
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom 6px
            font-size 24px
            margin-top 6px
    .backdrop
      background-color rgba(7, 17, 27, 0.6)
      position fixed
      top 0
      left 0
      height 100%
      width 100%
      z-index -2
      &.fade-enter-active,&.fade-leave-active
        transition all 0.5s
      &.fade-enter,&.fade-leave-active
        opacity 0
    .ball-container
      .ball
        position fixed
        left 32px
        bottom 22px
        z-index 200
        &.drop-enter,&.drop-enter-active
          transition all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            width 16px
            height 16px
            background-color rgb(0, 160, 220)
            border-radius 50%
            transition all 0.4s linear
</style>

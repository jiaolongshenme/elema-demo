
<template>
  <transition name="inner">
    <div v-show="showFlag" class="food-detial"  ref="foodscroll" >
      <div class="food-content" >
        <div class="icon_back" @click="showToggle">
          <i class="icon-arrow_lift"></i>
        </div>
        <div class="img-wrapper" >
          <img :src="food.image">
        </div>
        <div class="info">
          <div class="title">{{food.name}}</div>
          <div class="desc-wrapper">
            <span>月售{{food.sellCount}}份</span>
            <span>好评率{{food.rating}}%</span>
          </div>
          <div class="price-wrapper">
            <span class="price">￥<span class="price-number">{{food.price}}</span></span>
            <span v-show="food.oldPrice" class="old-price">￥{{food.oldPrice}}</span>
          </div>
          <transition name="fade">
            <div class="shopcart-wrapper" @click.stop.prevent="cartToggle($event)" v-show="cartShow">
              <span>加入购物车</span>
            </div>
          </transition>
          <transition name="fadein">
            <div class="control-wrapper" v-show="!cartShow">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </transition>
        </div>
        <split></split>
        <div class="desc">
          <div class="title">商品描述</div>
          <div class="content">{{food.info}}</div>
        </div>
        <split></split>
        <div class="evaluation">
          <div class="title">商品评价</div>
          <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
          :rating="food.ratings"></ratingselect>
          <div class="evel-list" >
            <ul>
              <li v-for="rating in food.ratings" class="rating border-1px" v-show="showRating(rating.rateType, rating.text)">
                <div class="user-info">
                  <div class="time">{{rating.rateTime}}</div>
                  <div class="user">
                    <span>{{rating.username}}<img :src="rating.avatar"/></span>
                  </div>
                </div>
                <div class="user-content">
                  <span :class="rating.rateType === 0?'icon-thumb_up':'icon-thumb_down'"></span>
                  {{rating.text}}
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </transition>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-Scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';
  import Vue from 'vue';

  const ALL = 2;
//  const POSITIVE = 1;
//  const NEGATIVE = 0;
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
        selectType: 2,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    computed: {
      cartShow () {
        if (this.food.count) {
          return false;
        }
        return true;
      }
    },
    created () {
      this.$root.eventHub.$on('ratingType.select', (type) => {
        this.selectType = type;
        this.$nextTick(() => {
          this.foodScroll.refresh();
        });
      });
      this.$root.eventHub.$on('content.toggle', (onlyContent) => {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.foodScroll.refresh();
        });
      });
    },
//    events: {
//      'ratingType.select' (type) {
//        this.selectType = type;
//      },
//      'content.toggle' (onlyContent) {
//        this.onlyContent = onlyContent;
//      }
//    },
    methods: {
      showRating (type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },

      cartToggle (event) {
        if (!event._constructed) {
          return;
        }
        Vue.set(this.food, 'count', 1);
      },
      showToggle () {
        this.showFlag = !this.showFlag;
//        this.selectType = 0;
//        this.onlyContent = false;

        if (this.showFlag) {
          this.$nextTick(() => {
            if (!this.foodScroll) {
              this.foodScroll = new BScroll(this.$refs.foodscroll, {
                click: true
              });
            } else {
              this.foodScroll.refresh();
            }
          });
        }
      }
    },
    components: {
      'cartcontrol': cartcontrol,
      'split': split,
      'ratingselect': ratingselect
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin";

.food-detial
  position fixed
  top 0
  left 0
  bottom 48px
  width 100%
  height 100%
  z-index 30
  background-color #fff
  &.inner-enter-active,&.inner-leave-active
    transition all 0.5s
  &.inner-enter,&.inner-leave-active
    transform translate3d(100%, 0, 0)
  .food-content
    position relative
    .icon_back
      position absolute
      padding 15px
      z-index 3
      .icon-arrow_lift
        font-size 18px
        color #ffffff
    .img-wrapper
      position relative
      width 100%
      height 0
      padding-top 100%
      & > img
        position absolute
        left 0
        top 0
        width 100%
        height 100%
    .info
      position relative
      padding 18px 18px
      .title
        font-size 14px
        font-weight 700
        line-height 14px
        color rgb(7, 17, 27)
      .desc-wrapper
        margin 8px 0 18px 0
        font-size 10px
        line-height 10px
        color rgb(147, 153, 159)
      .price-wrapper
        font-size 10px
        color rgb(240, 20, 20)
        font-weight normal
        line-height 24px
        .price-number
          font-size 14px
          font-weight 700
        .old-price
          color rgb(147, 153, 159)
          text-decoration line-through
      .shopcart-wrapper
        position absolute
        bottom 18px
        right 18px
        background-color rgb(0, 160, 220)
        padding 6px 12px 6px 12px
        border-radius 40px
        &.fade-enter-active,&.fade-leave-active
          transition all 0.5s
        &.fade-enter,&.fade-leave-active
          opacity 0
        & > span
          font-size 10px
          color #ffffff
      .control-wrapper
        position absolute
        bottom 12px
        right 12px
    .desc
      padding 18px 18px
      .title
        font-size 14px
        font-weight normal
        line-height 14px
      .content
        margin 6px 8PX 0 8PX
        font-size 12px
        line-height 24px
        color rgb(77, 85, 93)
    .evaluation
      padding 18px 0 60px 0 //地步掩盖
      .title
        padding-left 18px
        font-size 14px
        font-weight normal
        line-height 14px

      .evel-list
        margin 0 16px
        .rating
          padding 18px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .user-info
            display flex
            margin-bottom 6px
            font-size 10px
            color rgb(147, 153, 159)
            line-height 12px
            .time
              flex 1
            .user
              flex 1
              text-align right
              &>span
                &>img
                  width 12px
                  height 12px
                  padding-left 6px
                  border-radius 50%
          .user-content
            font-size 12px
            color rgb(7, 17, 27)
            line-height 16px
            .icon-thumb_up
              color rgb(0, 160, 220)
            .icon-thumb_down
              color rgb(147, 153, 159)

</style>

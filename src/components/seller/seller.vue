<template>
  <div class="sell" ref="sellWrapper">
    <div class="conwrapper">
      <div  class="sell-content">
        <div class="content-info border-1px">
          <h1 class="title">{{seller.name}}</h1>
          <div class="score-desc">
          <span class="star-line">
             <star :size="36" :score="seller.foodScore"></star>
          </span>
            <span class="ratingCount">({{seller.ratingCount}})</span>
            <span class="sellCount">月售{{seller.sellCount}}单</span>
          </div>
          <div class="collect" @click="collectToggle">
            <div class="icon">
              <span class="icon-favorite" :class="{'icon-favorite-hover':isActive}"></span>
            </div>
            <p>{{isActive?"已收藏":"收藏"}}</p>
          </div>
        </div>
        <div class="content-desc">
          <div class="count-wrapper">
            <h3>起送价</h3>
            <div class="count">
              <span>{{seller.minPrice}}</span>
              元
            </div>
          </div>
          <div class="count-wrapper">
            <h3>商家配送</h3>
            <div class="count">
              <span>{{seller.deliveryPrice}}</span>
              元
            </div>
          </div>
          <div class="count-wrapper">
            <h3>平均配送时间</h3>
            <div class="count">
              <span>{{seller.deliveryTime}}</span>
              元
            </div>
          </div>
        </div>
      </div>
      <split></split>
      <div class="activities">
        <h1 class="title">公告与活动</h1>
        <p class="desc">{{seller.bulletin}}</p>
        <ul class="bulletin">
          <li class="bulletin-item border-1px" v-for="item in seller.supports">
            <span class="icon" :class="classMap[item.type]"></span>
            <span class="item-desc">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="seller-photo">
        <h1 class="title">商家实景</h1>
        <div class="img-wrapper" ref="picsWrapper">
          <div calss="img-content" ref="picsList">
            <img class="photos" v-for="item in seller.pics" :src="item">
          </div>
        </div>
      </div>
      <split></split>
      <div class="seller-infos">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info border-1px" v-for="item in seller.infos">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-Scroll';
  import star from 'components/star/star';
  import split from 'components/split/split';

  const ERR_OK = 0;

  export default {
//    props: {
//      seller: {
//        type: Object
//      }
//    },
    data () {
      return {
        goods: [],
        isActive: false,
        seller: {}
      };
    },
    components: {
      'star': star,
      'split': split
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'guarantee', 'invoice'];
      this.$http.get('/api/seller').then(response => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.seller = response.data;
          this.$nextTick(() => {
            this.sellscroll = new BScroll(this.$refs.sellWrapper, {
              click: true
            });
            if (this.imgscroll) {
              return;
            }
            this.$refs.picsList.style.width = 120 * this.seller.pics.length + 6 * (this.seller.pics.length - 1) + 'px';
            this.imgscroll = new BScroll(this.$refs.picsWrapper, {
              click: true,
              scrollX: true,
              scrollY: false
            });
          });
        }
      });
    },
    methods: {
      collectToggle () {
        this.isActive = !this.isActive;
      }
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .sell
    position absolute
    top 174px
    left 0
    bottom 0
    width 100%
    overflow hidden
    .sell-content
      .content-info
        padding 18px 18px
        border-1px(rgba(7, 17, 27, 0.1))
        .title
          font-size 14px
          color rgb(7, 17, 27)
          line-height 14px
        .score-desc
          margin-top 8px
          font-size 0
          position relative
          .star-line
            display inline-block
            vertical-align top
          .ratingCount
            display inline-block
            margin 0 12px 0 8px
            font-size 10px
            color rgb(77, 85, 93)
            line-height 18px
          .sellCount
            display inline-block
            font-size 10px
            color rgb(77, 85, 93)
            line-height 18px
        .collect
          position absolute
          right 18px
          top 14px
          width 50px
          text-align center
          .icon
            font-size 24px
            line-height 24px
            margin-bottom 0
            .icon-favorite
              color rgba(7, 17, 27, 0.1)
              &.icon-favorite-hover
                color rgb(240, 20, 20)
              &>p
                font-size 10px
                color rgb(77, 85, 93)
                line-height 10px
      .content-desc
        display flex
        .count-wrapper
          flex 1
          text-align center
          margin 18px 0
          border-left 1px solid rgba(7, 17, 27, 0.1)
          & > h3
            font-size 10px
            line-height 10px
            color rgb(147, 153, 159)
            font-weight normal
            margin-bottom 4px
          .count
            font-size 10px
            & > span
              font-size 24px
              color rgb(7, 17, 27)
    .activities
      margin 18px 18px 0 18px
      .title
        font-size 14px
        color rgb(7, 17, 27)
        line-height 14px
        font-weight normal
      .desc
        padding 8px 12px 18px 12px
        font-size 12px
        color rgb(240, 20, 20)
        line-height 24px
        border-1px(rgba(7, 17, 27, 0.1))
      .bulletin
        .bulletin-item
          padding 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            border-none()
          .icon
            display inline-block
            vertical-align top
            width 16px
            height  16px
            background-size 16px 16px
            background-repeat no-repeat
            margin-right 6px
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .desc
            font-size 12px
            font-weight 200
            color rgb(7, 17, 27)
            line-height 16px
    .seller-photo
      margin 18px
      .title
        font-size 14px
        color rgb(7, 17, 27)
        line-height 14px
        font-weight normal
      .img-wrapper
        margin-top 12px
        width 100%
        height 90px
        overflow hidden
        .photos
          width 120px
          height 90px
          margin-right 6px
          &:last-child
            margin-right 0
    .seller-infos
      margin 18px 18px 0 18px
      .title
        font-size 14px
        color rgb(7, 17, 27)
        line-height 14px
        font-weight normal
        padding-bottom 12px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
      .info
        font-size 12px
        line-height 16px
        color rgb(7, 17, 27)
        padding 16px 12px 16px 12px
        border-1px(rgba(7, 17, 27, 0.1))
</style>

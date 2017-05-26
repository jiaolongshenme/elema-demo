<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img  :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
        <div class="support" v-if="seller.supports">

          <span class="text"><span class="icon" :class="this.classMap[seller.supports[0].type]"></span>{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="ShowDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="ShowDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow" >
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1>{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-show="seller.supports" class="supports" >
              <li v-for="item in seller.supports" class="supports-item">
                <span class="icon" :class="classMap[item.type]"></span>
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <p class="bulletin">{{seller.bulletin}}</p>
          </div>
        </div>

        <div class="detail-close">
          <i class="icon-close" @click="detailClose"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      'star': star
    },
    data () {
      return {
        detailShow: false
      };
    },
    methods: {
      ShowDetail () {
        this.detailShow = true;
      },
      detailClose () {
        this.detailShow = false;
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'guarantee', 'invoice'];
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .header
    position: relative
    color: #ffffff
    background: rgba(7, 17, 27, 0.5)
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px;
      display: flex
      .avatar
        width: 64px
        height: 64px
        & > img
          width: 64px
          height: 64px
          border-radius: 2px
      .content
        margin-left: 16px
        .title
          font-size: 16px
          line-height: 18px
          font-weight: bold
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            vertical-align: top
            width: 30px
            height: 18px
            margin-right: 6px
            bg-image ('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            font-size: 16px
            line-height: 18px
            font-weight: bold
      .description
        font-size: 12px
        font-weight: 200
        line-height: 12px
        padding-bottom: 10px
      .support
        font-size: 0
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 4px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_1')
          &.discount
            bg-image('discount_1')
          &.guarantee
            bg-image('guarantee_1')
          &.invoice
            bg-image('invoice_1')
          &.special
            bg-image('special_1')
        .text
          font-size: 10px
          line-height: 12px
      .support-count
        position: absolute
        height: 24px
        line-height: 24px
        bottom: 18px
        right: 12px
        padding: 0 8px
        background-color: rgba(0, 0, 0, 0.2)
        border-radius: 14px
        text-align: center
        .count
          font-size: 10px
        .icon-keyboard_arrow_right
          font-size: 10px
    .bulletin-wrapper
      position: relative
      height: 28px
      padding: 0 22px 0 12px
      background: rgba(7, 17, 27, 0.2)
      line-height: 28px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      .bulletin-title
        display: inline-block
        vertical-align: top
        width: 22px
        height: 28px
        bg-image('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
        background-position : 0 8px
      .bulletin-text
        font-size: 10px
        line-height: 28px
        margin: 0 4px
      .icon-keyboard_arrow_right
        position: absolute
        top: 8px
        right: 12px
        font-size: 12px
        margin-left: 2px
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      overflow: hidden
      filter: blur(10px)
      & > img
        width: 100%
        height: 100%
    .detail
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      background: rgba(7, 17, 27, 0.8)
      z-index: 100
      overflow : auto
      backdrop-filer: blur(10px)
      transition: all 0.5s
      &.fade-transition
        opacity : 1
        background: rgba(7, 17, 27, 0.8)
      &.fade-enter,&.fade-leave-to
        opacity : 0
        background: rgba(7, 17, 27, 0)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          & > h1
            font-size: 16px
            line-height: 16px
            text-align : center
          .star-wrapper
            text-align : center
            margin: 18px auto 28px auto
          .title
            width: 80%
            margin: 28px auto 24px auto
            display: flex
            .line
              flex: 1
              position: relative
              top: -6px
              border-bottom: 1px solid rgba(225, 225, 225, 0.2)
            .text
              font-size: 14px
              line-height: 14px
              font-weight: 700
              padding: 0 12px
          .supports
            padding: 0 36px 0 36px
            width: 80%
            font-size: 0
            .supports-item
              padding: 0 12px
              margin-bottom: 12px
              &:last-child
                margin-bottom: 0
              .icon
                display: inline-block
                vertical-align: top
                width: 16px
                height: 16px
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                font-size: 12px
                line-height: 16px
          .bulletin
            font-size: 12px
            line-height: 24px
            padding: 0 12px 0 12px
            margin : 0 36px 0 36px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        .icon-close
          font-size: 32px
          color: rgba(255, 255, 255, 0.5)
</style>

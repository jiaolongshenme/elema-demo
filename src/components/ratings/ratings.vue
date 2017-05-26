<template>
  <div class="ratings" ref="ratingsWrapper">
    <div class="contentwrapper">
      <div class="rating-wrapper">
        <div class="rating-left">
          <h1 class="score">{{seller.score}}</h1>
          <h3 class="text">综合评分</h3>
          <p class="rankRate">高于周边商家{{seller.rankRate}}%</p>
        </div>
        <div class="rating-right">
          <div class="serviceScore">
            <span class="title">服务态度</span>
          <span class="starline">
            <star :size="36" :score="seller.serviceScore"></star>
          </span>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="foodScore">
            <span class="title">美味等级</span>
          <span class="starline">
            <star :size="36" :score="seller.foodScore"></star>
          </span>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="deliveryTime">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                    :rating="ratings"></ratingselect>
      <div class="rating-list">
        <ul>
          <li class="rating-item border-1px" v-for="rating in ratings" v-show="ratingsShow(rating.rateType, rating.text)">
            <div class="rating-avatar"><img :src="rating.avatar"/></div>
            <div class="rating-content">
              <div class="user-info">
                <span class="user-name">{{rating.username}}</span>
                <span class="timeline">{{rating.rateTime}}</span>
              </div>
              <div class="rating-info">
                <span class="star-wrapper"><star :size="24" :score="rating.score"></star></span>
                <span class="deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <div class="rating-text">{{rating.text}}</div>
              <div class="recommend">
                <span class="icon" :class="rating.rateType === 0?'icon-thumb_up':'icon-thumb_down'"></span>
                <span class="dish" v-for="item in rating.recommend">{{item}}</span>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-Scroll';
  import star from 'components/star/star';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';

  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: 2,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      };
    },
    components: {
      'split': split,
      'ratingselect': ratingselect,
      'star': star
    },
    created () {
      this.$http.get('/api/ratings').then(response => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
            this.ratingscroll = new BScroll(this.$refs.ratingsWrapper, {
              click: true
            });
          });
        }
      });
      this.$root.eventHub.$on('ratingType.select', (type) => {
        this.selectType = type;
        this.$nextTick(() => {
          this.ratingscroll.refresh();
        });
      });
      this.$root.eventHub.$on('content.toggle', (onlyContent) => {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.ratingscroll.refresh();
        });
      });
    },
    methods: {
      ratingsShow (type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === 2) {
          return true;
        } else {
          return this.selectType === type;
        }
      }
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .ratings
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .rating-wrapper
      margin 0 0
      display flex
      .rating-left
        margin 18px 0
        flex 0 0 137px
        width 137px
        text-align center
        border-right 1px solid rgba(7, 17, 27, 0.1)
        .score
          font-size 24px
          line-height 28px
          color rgb(255, 153, 0)
          font-weight 200
        .text
          font-size 12px
          line-height 12px
          margin 6px 0 8px 0
        .rankRate
          font-size 10px
          color rgb(7, 17, 27)
          line-height 10px
      .rating-right
        flex 1
        padding 18px 24px
        .serviceScore, .foodScore, .deliveryTime
          font-size 0
          display flex
          margin-bottom 8px
          .title
            vertical-align top
            font-size 12px
            color rgb(7, 17, 27)
            line-height 18px
            margin-right 12px
          .score
            vertical-align top
            padding-left 12px
            font-size 12px
            line-height 18px
            color rgb(255, 153, 0)
          .time
            font-size 12px
            color rgb(147, 153, 159)
            line-height 18px
        .deliveryTime
          margin-bottom 0



    .rating-list
      margin 0 18px
      .rating-item
        border-1px(rgba(7, 17, 27, 0.1))
        display flex
        padding 18px 0
        .rating-avatar
          flex 0 0 28px
          weight 28px
          &>img
            width 28px
            height 28px
            border-radius 50%
        .rating-content
          flex 1
          margin-left 12px
          .user-info
            display flex
            .user-name
              flex 1
              font-size 10px
              line-height 12px
              color rgb(7, 17, 27)
            .timeline
              flex 1
              text-align  right
              font-size 10px
              line-height 12px
              color rgb(147, 153, 159)

          .rating-info
            margin 4px 6px 6px 0
            .star-wrapper
              display inline-block
            .deliveryTime
              font-size 10px
              color rgb(147, 153, 159)
          .rating-text
            font-size 12px
            color rgb(7, 17, 27)
            line-height 18px
            margin-bottom 8px
          .recommend
            .icon-thumb_up
              font-size 12px
              color rgb(0, 160, 220)
              line-height 16px
            .icon-thumb_down
              color rgb(147, 153, 159)
            .dish
              font-size 9px
              color rgb(147, 153, 159)
              line-height 16px
              border 1px solid rgba(7, 17, 27, 0.1)
              border-radius 2px
              padding 2px 6px
              margin-right 8px

</style>

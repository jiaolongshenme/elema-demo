<template>
  <div class="ratingselect">
    <div class="classify border-1px">
      <span @click="select(2,$event)" class="all" :class="{'active':selectType === 2}">{{desc.all}}<span>{{rating.length}}</span></span>
      <span @click="select(0,$event)" class="good" :class="{'active':selectType === 0}">{{desc.positive}}<span>{{positives.length}}</span></span>
      <span @click="select(1,$event)" class="bad" :class="{'active':selectType === 1}">{{desc.negative}}<span>{{negatives.length}}</span></span>
    </div>
    <div class="switch">
          <span class="icon" @click="switched($event)">
            <i class="icon-check_circle" :class="{'on':onlyContent === true}"></i>
          </span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const ALL = 2;
  const POSITIVE = 0;
  const NEGATIVE = 1;

  export default {
    props: {
      rating: {
        type: Array
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return;
        }
        this.selectType = type;
        this.$root.eventHub.$emit('ratingType.select', type);
      },
      switched (event) {
        if (!event._constructed) {
          return;
        }
        this.onlyContent = !this.onlyContent;
        this.$root.eventHub.$emit('content.toggle', this.onlyContent);
      }
    },
    computed: {
      positives () {
        return this.rating.filter((data) => {
          return data.rateType === POSITIVE;
        });
      },
      negatives () {
        return this.rating.filter((data) => {
          return data.rateType === NEGATIVE;
        });
      }
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .classify
    font-size 0
    padding 18px 0
    margin 0 18px
    border-1px(rgba(7, 17, 27, 0.1))
    &>span
      display inline-block
      font-size 12px
      margin-right 8px
      padding 8px 12px
    .all
      background-color rgba(0, 160, 220, 0.2)
      color rgb(77, 85, 93)
      &.active
        background-color rgb(0, 160, 220)
        color #ffffff
    .good
      background-color rgba(0, 160, 220, 0.2)
      color rgb(77, 85, 93)
      &.active
        background-color rgb(0, 160, 220)
        color #ffffff
    .bad
      background-color rgba(77, 85, 93, 0.2)
      color rgb(77, 85, 93)
      &.active
        background-color rgb(77, 85, 93)
        color #ffffff
  .switch
    padding  12px 18px
    font-size 12px
    line-height 24px
    color rgb(147, 153, 159)
    border-bottom 1px solid rgba(7, 17, 27, 0.1)
    .icon
      display inline-block
      font-size 24px
      vertical-align top
      margin-left 4px
      .icon-check_circle
        color rgb(147, 153, 159)
        &.on
          color #00c850
</style>

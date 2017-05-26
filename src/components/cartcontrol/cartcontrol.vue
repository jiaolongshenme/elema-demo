<template>
  <div class="cartcontrol">
    <transition name="move">
      <div v-show="food.count>0" @click.stop.prevent="decreaseCart()" class="cart-decrease">
        <i class="icon-remove_circle_outline"></i>
      </div>
    </transition>
    <div v-show="food.count" class="cart-count">{{food.count}}</div>
    <div class="cart-add" @click.stop.prevent="addCart($event)">
      <i class="icon-add_circle"></i>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart (event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$root.eventHub.$emit('cart.add', event.target);
      },
      decreaseCart () {
        if (!event._constructed) {
          return;
        }
        if (this.food.count > 0) {
          this.food.count--;
        }
      }
    }
  };
</script >

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size 0
    .cart-decrease
      display inline-block
      padding 6px
      transition all 0.4s
      .icon-remove_circle_outline
        display inline-block
        font-size 24px
        line-height 24px
        color rgb(0, 160, 220)
        transition all 0.4s
      &.move-transition
        opacity 1
        transform translate3D(0, 0, 0)
        .icon-remove_circle_outline
          transform rotate(0)
      &.move-enter,&.move-leave-to
        opacity 0
        transform translate3D(24px, 0, 0)
        .icon-remove_circle_outline
          transform rotate(180deg)
    .cart-count
      display inline-block
      vertical-align top
      font-size 10px
      line-height 24px
      padding 6px 0
      width 12px
      color rgb(77, 85, 93)
      text-align center
    .cart-add
      padding 6px
      display inline-block
      .icon-add_circle
        font-size 24px
        line-height 24px
        color rgb(0, 160, 220)
</style>

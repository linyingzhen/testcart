<template lang="html">

  <div class="cartcontrol">
    <transition name="fadeRotate">
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart()">
          <span class="icon-remove_circle_outline inner"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">
      {{food.count}}
    </div>
    <div class="cart-add" @click.stop.prevent="addCart($event)">
      <i class="icon-add_circle"></i>
    </div>
  </div>

</template>

<script>
import Vue from 'vue'

export default {
  props: {
    food: Object,
    selectFoods:Array
  },
  methods: {
    addCart(event) {
      // console.log(event.target);
      if (!event._constructed) {
        return
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 0)
      }
      this.food.count++;
      this.$root.eventHub.$emit('cart.add', event.target); 
    },
    decreaseCart() {
      if (!event._constructed || !this.food.count) {
        return
      }
      this.food.count--;
      if(this.food.count<=0){
          if(this.selectFoods.length==1){
            this.$emit('qkgwc')
          }
      }
    },
  }
}

</script>

<style lang="stylus">

.cartcontrol
  .cart-decrease
    font-size .30rem
    display inline-block
    padding .12rem
    height: 24px
    .inner
      line-height 24px
      font-size 24px
      color #cccccc
      transition all 0.4s linear
    &.fadeRotate-enter-active, &.fadeRotate-leave-active
      transform translate3d(0,0,0)
      .inner
        display inline-block
        transform rotate(0)
    &.fadeRotate-enter, &.fadeRotate-leave-active
      opacity: 0
      transform translate3d(100px,0,0)
      .inner
        transform rotate(180deg)
  .cart-count
    display inline-block
    vertical-align top
    font-size .26rem
    color rgb(147,153,159)
    line-height 24px
    text-align center
    padding .12rem 0
  .cart-add
    display inline-block
    vertical-align top
    font-size 24px
    color #005e32
    line-height 24px
    height: 24px
    padding .12rem
</style>

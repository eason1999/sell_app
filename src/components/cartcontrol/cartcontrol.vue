<template>
  <div class="cart-control">
    <div class="decrease" v-show="food.count>0" transition="move" @click.stop.prevent="decCart">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="num" v-show="food.count>0">{{food.count}}</div>
    <div class="add icon-add_circle" @click.stop.prevent="addCart"></div>
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
    created() {

    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$dispatch('cart.add', event.target);
      },
      decCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count--;
        } else {
          return;
        }
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .cart-control
    font-size: 0
    .decrease
      display: inline-block
      padding: 6px
      transition: all 0.4s linear
      &.move-transition
        opacity: 1
        transform: translate3d(0, 0, 0)
        .inner
          display: inline-block
          line-height: 24px
          font-size: 24px
          color: rgb(0, 160, 220)
          transition: all 0.4s linear
          transform: rotate(0)
      &.move-enter, &.move-leave
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform: rotate(360deg)
    .num
      display: inline-block
      font-size: 12px
      width: 12px
      vertical-align: top
      padding-top: 6px
      line-height: 24px
      text-align: center
      color: rgb(147, 153, 159)
    .add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
</style>

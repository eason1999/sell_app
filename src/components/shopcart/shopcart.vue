<template>
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'high-light':totalCount>0}">
            <span class="icon-shopping_cart"></span>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'high-light':totalPrice>0}">￥{{totalPrice}}元</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.prevent="pay" :class="{'high-light':hightLight}">{{payDesc}}</div>
    </div>
    <div class="ball-wrapper">
      <div transition="drop" class="ball" v-for="item in balls" v-show="item.show">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="list-content" v-el:list-content>
        <ul>
          <li class="food border-one" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>￥{{food.price*food.count}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="list-mask" v-show="listShow" transition="fade" @click="hideList"></div>
</template>
<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
  import BetterScroll from 'better-scroll';
  export default {
    props: {
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      },
      selectFoods: {
        type: Array,
        default() {
          return [];
        }
      }
    },
    components: {
      cartcontrol
    },
    data() {
      return {
        balls: [
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ],
        dropBall: [],
        fold: true
      };
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            // 避免重复渲染
            if (!this.scroll) {
              this.scroll = new BetterScroll(this.$els.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      },
      hightLight() {
        let item = (this.totalPrice >= this.minPrice);
        return item;
      }
    },
    methods: {
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      hideList() {
        this.fold = true;
      },
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBall.push(ball);
            return;
          }
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`支付${this.totalPrice}元`);
      }
    },
    transitions: {
      drop: {
        beforeEnter(el) {
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
              let react = ball.el.getBoundingClientRect();
              let x = react.left - 32;
              let y = -(window.innerHeight - react.top - 22);
              el.style.display = '';
              el.style.webkitTransform = `translate3d(0,${y}px,0)`;
              el.style.transform = `translate3d(0,${y}px,0)`;
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
              inner.style.transform = `translate3d(${x}px,0,0)`;
            }
          }
        },
        enter(el) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)';
            el.style.transform = 'translate3d(0,0,0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translate3d(0,0,0)';
            inner.style.transform = 'translate3d(0,0,0)';
          });
        },
        afterEnter(el) {
          let ball = this.dropBall.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        }
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 40
    backdrop-filter: blur(10px)
    transition: all 0.5s
    &.fade-transition
      opacity: 1
      background: rgba(7, 17, 27, 0.6)
    &.fade-enter, &.fade-leave
      opacity: 0
      background: rgba(7, 17, 27, 0)

  .shopcart
    position: fixed
    z-index: 101
    bottom: 0
    left: 0
    width: 100%
    height: 48px
    .content
      display: flex
      font-size: 0
      background: #141d27
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          border-radius: 50%
          box-sizing: border-box
          background: #141d27
          .logo
            height: 100%
            width: 100%
            background: rgba(255, 255, 255, 0.1)
            border-radius: 50%
            text-align: center
            &.high-light
              background: rgb(0, 160, 220)
              .icon-shopping_cart
                color: rgb(255, 255, 255)
            .icon-shopping_cart
              font-size: 24px
              color: #80858a
              line-height: 44px
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            font-size: 10px
            font-weight: 700
            color: rgb(255, 255, 255)
            line-height: 16px
            background: rgb(240, 20, 20)
            text-align: center
            border-radius: 16px
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin-top: 12px
          box-sizing: border-box
          padding-right: 12px
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 14px
          font-weight: 700
          color: rgba(255, 255, 255, 0.4)
          &.high-light
            color: rgb(255, 255, 255)
        .desc
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin: 12px 0 0 12px
          font-size: 12px
          color: rgba(255, 255, 255, 0.4)
          font-weight: 200
      .content-right
        flex: 0 0 105px
        width: 105px
        background: rgba(255, 255, 255, 0.1)
        font-size: 12px
        color: rgba(255, 255, 255, 0.6)
        line-height: 48px
        text-align: center
        font-weight: 700
        &.high-light
          background: #00b43c
          color: rgb(255, 255, 255)
    .ball-wrapper
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-transition
          transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s linear
    .shopcart-list
      position: absolute
      top: 0
      left: 0
      z-index: -1
      width: 100%
      &.fold-transition
        transition: all 0.5s
        transform: translate3d(0, -100%, 0)
      &.fold-enter, &.fold-leave
        transform: translate3d(0, 0, 0)
      .list-header
        height: 40px
        padding: 0 18px
        line-height: 40px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          font-size: 14px
          font-weight: 200
          color: rgb(7, 17, 27)
          float: left
          width: auto
        .empty
          font-size: 12px
          color: rgb(0, 160, 220)
          float: right
      .list-content
        padding: 0 18px
        max-height: 217px
        background: #fff
        overflow: hidden
        .food
          position: relative
          box-sizing: border-box
          height: 48px
          padding: 12px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            font-size: 14px
            font-weight: 700
            color: rgb(7, 17, 27)
            line-height: 24px
          .price
            position: absolute
            right: 90px
            bottom: 12px
            font-size: 12px
            color: rgb(240, 20, 20)
            margin-right: 12px
            font-weight: 700
            line-height: 24px
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 6px
</style>

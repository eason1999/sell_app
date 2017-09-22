<template>
  <div class="food" v-show="showFlag" transition="move" v-el:food>
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image"/>
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"> </i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="newPrice">￥{{food.price}}</span><span class="oldPrice"
                                                             v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0" transition="fade">加入购物车
        </div>
      </div>
      <split v-show="food.info"></split>
      <div class="info" v-show="food.info">
        <h2 class="title">商品信息</h2>
        <p class="text">{{food.info}}</p>
      </div>
      <split></split>
      <div class="ratings">
        <h2 class="title">商品评价</h2>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                      :ratings="food.ratings"></ratingselect>
        <div class="ratings-wrapper">
          <ul v-show="food.ratings&&food.ratings.length">
            <li v-for="rating in food.ratings" class="rating-item border-1px"
                v-show="needShow(rating.rateType,rating.text)">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img class="avatar" width="12" height="12" :src="rating.avatar"/>
              </div>
              <div class="time">{{rating.rateTime | formDate}}</div>
              <p class="text">
                <span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType === 1}"></span>
                {{rating.text}}
              </p>
            </li>
          </ul>
          <div v-show="!food.ratings||!food.ratings.length" class="no-ratings">暂无评价</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import {formatDate} from 'common/js/date.js';
  import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
  import split from 'components/split/split.vue';
  import ratingselect from 'components/ratingselect/ratingselect.vue';

  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    filters: {
      formDate(value) {
        let date = new Date(value);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        }
        this.$dispatch('cart.add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return this.selectType === type;
        }
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .food
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 30
    width: 100%
    background: #fff
    &.move-transition
      transform: translate3d(0, 0, 0)
      transition: all 0.2s linear
    &.move-enter, &.move-leave
      transform: translate3d(100%, 0, 0)
    .image-header
      position: relative
      width: 100%
      height: 0
      padding-bottom: 100%
      img
        position: absolute
        width: 100%
        height: 100%
        top: 0
        left: 0
      .back
        position: absolute
        top: 10px
        left: 0
        .icon-arrow_lift
          display: block
          padding: 10px
          font-size: 20px
          color: #fff
    .content
      position: relative
      padding: 18px
      .title
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
        line-height: 14px
        margin-bottom: 8px
      .detail
        font-size: 10px
        color: rgb(147, 153, 159)
        line-height: 10px
        margin-bottom: 18px
        height: 10px
        .sell-count
          margin-right: 12px
      .price
        .newPrice
          font-size: 14px
          font-weight: 700
          color: rgb(240, 20, 20)
          line-height: 24px
        .oldPrice
          font-size: 10px
          font-weight: 700
          color: rgb(147, 153, 159)
          line-height: 24px
    .cartcontrol-wrapper
      position: absolute
      right: 12px
      bottom: 12px
    .buy
      position: absolute
      right: 18px
      bottom: 18px
      z-index: 10
      width: 74px
      height: 24px
      padding: 6px
      box-sizing: border-box
      text-align: center
      color: #ffffff
      border-radius: 12px
      background: rgb(0, 160, 220)
      font-size: 10px
      &.fade-transition
        transition: all 0.5s
        opacity: 1
      &.fade-enter, &.fade-leave
        opacity: 0
    .info
      padding: 18px
      .title
        margin-bottom: 6px
        font-weight: 200
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .text
        font-size: 12px
        font-weight: 200
        color: rgb(77, 85, 93)
        line-height: 24px
        padding: 0 8px
    .ratings
      padding-top: 18px
      .title
        margin-left: 18px
        font-weight: 200
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .ratings-wrapper
        padding: 0 18px
        .rating-item
          position: relative
          padding: 16px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .user
            position: absolute
            right: 0
            top: 16px
            font-size: 0
            line-height: 12px
            .name
              margin-right: 6px
              font-size: 10px
              color: rgb(147, 153, 159)
            .avatar
              border-radius: 50%
              vertical-align: top
          .time
            margin-bottom: 6px
            font-size: 10px
            color: rgb(147, 153, 159)
            line-height: 12px
          .text
            font-size: 12px
            line-height: 16px
            color: rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              line-height: 16px
              margin-right: 4px
              font-size: 12px
            .icon-thumb_down
              color: rgb(147, 153, 159)
            .icon-thumb_up
              color: rgb(0, 160, 220)
        .no-ratings
          padding: 16px 0
          font-size: 12px
          color: rgb(147, 153, 159)
</style>

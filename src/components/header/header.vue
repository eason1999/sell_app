<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetial">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetial">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <span class="icon-keyboard_arrow_right"></span>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
    <div class="detail" v-show="detailShow" transition="fade">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size='48' :score='seller.score'></star>
          </div>
          <div class="top-wrapper">
            <line :text=datas></line>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li v-for="item in seller.supports" class="supports-item">
              <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
              <span class="text">{{item.description}}</span>
            </li>
          </ul>
          <div class="top-wrapper">
            <line :text=dataw></line>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="closeDetail">
        <span class="icon-close"></span>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star';
  import line from '../../components/lines/line';
  export default {
    props: {
      seller: {
        type: Object,
        default: {}
      }
    },
    components: {
      star,
      line
    },
    data() {
      return {
        classMap: [],
        detailShow: false,
        datas: '优惠信息',
        dataw: '商家公告'
      };
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    methods: {
      showDetial() {
        this.detailShow = true;
      },
      closeDetail() {
        this.detailShow = false;
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .header
    color: #fff
    overflow: hidden
    background: rgba(7, 17, 27, 0.5)
    position: relative
    .content-wrapper
      padding: 24px 12px 18px 24px
      font-size: 0
      position: relative
      .avatar
        display: inline-block
        vertical-align: top
        img
          border-radius: 2px
      .content
        display: inline-block
        margin-left: 16px
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            vertical-align: top
            border-radius: 2px
            width: 30px
            height: 18px
            bg-img('brand')
            background-size: 30px 18px
            background-repeat: no-repeat;
          .name
            font-size: 16px
            font-weight: 700
            margin-left: 6px
            line-height: 18px
        .description
          margin-bottom: 10px
          line-height: 12px
          font-size: 12px
        .support
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-img('decrease_1')
            &.discount
              bg-img('discount_1')
            &.guarantee
              bg-img('guarantee_1')
            &.invoice
              bg-img('invoice_1')
            &.special
              bg-img('special_1')
          .text
            font-size: 10px
            line-height: 12px
      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        border-radius: 14px
        background: rgba(0, 0, 0, 0.2)
        text-align: center
        .count
          vertical-align: top
          line-height: 26px
          font-size: 10px
        .icon-keyboard_arrow_right
          margin-left: 2px
          line-height: 25px
          font-size: 10px
    .bulletin-wrapper
      position: relative
      height: 28px
      padding: 0 22px 0 12px
      overflow: hidden
      white-space: nowrap
      text-overflow: ellipsis
      background: rgba(7, 17, 27, 0.2)
      .bulletin-title
        display: inline-block
        vertical-align: top
        margin-top: 8px
        width: 22px
        height: 12px
        bg-img('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        font-size: 10px
        font-weight: 200
        line-height: 30px
        margin: 0 4px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 10px
        top: 10px
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .detail
      position: fixed
      top: 0
      left: 0
      z-index: 1000
      width: 100%
      height: 100%
      overflow: auto
      backdrop-filter: blur(10px)
      transition: all 0.4s
      &.fade-transition
        opacity: 1
        background: rgba(7, 17, 27, 0.8)
      &.fade-enter, &.fade-leave
        opacity: 0
        background: rgba(7, 17, 27, 0)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            font-weight: 700
          .star-wrapper
            margin-top: 18px
            padding: 2px 0
            text-align: center
          .top-wrapper
            width: 80%
            margin: 28px auto 24px auto
          .bulletin
            width: 80%
            margin: 0 auto
            .content
              padding: 0 12px
              line-height: 24px
              font-size: 12px
          .supports
            margin: 0 auto
            width: 80%
            padding: 0 12px
            box-sizing: border-box
            .supports-item
              font-size: 0
              font-weight: 200
              margin-bottom: 12px
              color: rgb(255, 255, 255)
              &:last-child
                margin-bottom: 0
              .icon
                display: inline-block
                height: 16px
                width: 16px
                vertical-align: top
                background-repeat: no-repeat
                background-size: 16px 16px
                &.decrease
                  bg-img('decrease_2')
                &.discount
                  bg-img('discount_2')
                &.guarantee
                  bg-img('guarantee_2')
                &.invoice
                  bg-img('invoice_2')
                &.special
                  bg-img('special_2')
              .text
                margin-left: 6px
                font-size: 12px
                line-height: 16px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        .icon-close
          font-size: 32px
</style>

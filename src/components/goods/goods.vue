<template>
  <div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{current:currentIndex==$index}"
            @click="selectMenu($index,$event)">
          <span class="text border-one"><span v-show="item.type>0" :class="classMap[item.type]"
                                              class="icon"> </span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" v-el:food-wrapper>
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item border-one">
              <div class="icon">
                <img :src="food.icon" width="57" height="57"/>
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="sellCount">月售{{food.sellCount}}份</span>
                  <span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="newPrice">￥{{food.price}}</span>
                  <span class="oldPrice" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="control-wrapper">
                  <cartcontrol :food="food"> </cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"> </shopcart>
    <foods :food="selectedFood" v-ref:foods> </foods>
  </div>
</template>
<script type="text/ecmascript-6">
  import BetterScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart.vue';
  import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
  import foods from 'components/food/food.vue';
  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      shopcart,
      cartcontrol,
      foods
    },
    data() {
      return {
        goods: [],
        classMap: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
        listHeight: [],
        scrollY: 0,
        selectFoods: [],
        selectedFood: {}
      };
    },
    events: {
      'cart.add'(target) {
        this._drop(target);
      }
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created() {
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        }
      });
    },
    methods: {
      _drop(target) {
        // 体验优化，动画优化
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      },
      _initScroll() {
        this.menuScroll = new BetterScroll(this.$els.menuWrapper, {
          click: true
        });
        this.foodScroll = new BetterScroll(this.$els.foodWrapper, {
          probeType: 3,
          click: true
        });
        this.foodScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight() {
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodScroll.scrollToElement(el, 300);
      },
      selectFood(food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.foods.show();
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl';
  .goods
    display: flex
    position: absolute
    top: 174px
    left: 0
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      width: 80px
      flex: 0 0 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        margin: 0 auto
        &.current
          position: relative
          z-index: 100
          background: #fff
          margin-top: -1px
          padding: 0 12px
          .text
            font-weight: 700
            border-none()
        &:last-child
          .text
            border-none()
        .text
          font-size: 12px
          display: table-cell
          width: 56px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-img('decrease_3')
          &.discount
            bg-img('discount_3')
          &.guarantee
            bg-img('guarantee_3')
          &.invoice
            bg-img('invoice_3')
          &.special
            bg-img('special_3')

  .foods-wrapper
    flex: 1
    .food-list
      .title
        height: 26px
        line-height: 26px
        background: #f3f5f7
        border-left: 2px solid #d9dde1
        font-size: 14px
        color: rgb(147, 153, 159)
        padding-left: 14px
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          margin-bottom: 0
          border-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
          border-radius: 2px
          img
            border-radius: 2px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            font-size: 14px
            color: rgb(7, 17, 27)
            line-height: 14px
          .desc
            font-size: 12px
            color: rgb(147, 153, 159)
            line-height: 14px
          .extra
            margin-top: 8px
            color: rgb(147, 153, 159)
            line-height: 10px
            font-size: 0
            .sellCount
              font-size: 12px
              margin-right: 12px
            .rating
              font-size: 12px
          .price
            font-size: 0
            .newPrice
              font-size: 14px
              color: rgb(240, 20, 20)
              font-weight: 700
              line-height: 24px
              margin-right: 8px
            .oldPrice
              font-size: 10px
              color: rgb(147, 153, 159)
              font-weight: 200
              line-height: 24px
              text-decoration: line-through
          .control-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>

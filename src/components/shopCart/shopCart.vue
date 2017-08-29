<template lang="html">
    <div class="">
        <div class="shopCart">
            <div class="content">
                <div class="content-left" @click="listToggle">
                    <div class="logo-wrapper">
                        <div class="badge" v-show="totalCount" :class="{'cartdown':listShow}">
                        </div>
                        <div class="logo" :class="{'active':totalPrice}">
                            <i class="lindeiconcart"></i>
                        </div>
                    </div>
                    <div v-if="totalPrice>0" class="price" :class="{'active':totalPrice}">
                        <p>合计：&yen{{totalPrice}}</p>
                        <p class="small">总共：{{totalCount}}件商品</p>
                    </div>
                    <div v-else class="price" :class="{'active':totalPrice}">
                        购物车是空的
                    </div>
                    <!-- <div class="desc">
          另需要配送费￥{{deliveryPrice}}元
        </div> -->
                </div>
                <div @click="goPay" class="content-right" :class="{'enough':totalPrice>minPrice}">
                    {{payDesc}}
                </div>
            </div>
            <div class="ball-container">
                <transition name="drop" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter" v-for="(ball,index) in balls">
                    <div class="ball" v-show="ball.show">
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
            </div>
            <transition name="transHeight">
                <div class="shopcart-list" v-show="listShow">
                    <div class="list-header">
                        <h1 class="title">购物车</h1>
                        <span class="empty" @click="setEmpty()">清空</span>
                    </div>
                    <div class="list-content" ref="foodlist">
                        <ul>
                            <li class="food" v-for="food in selectFoods">
                                <div v-if="food.totalprice">
                                    <span class="name">{{food.name}}</span>
                                    <div class="price">
                                        <span>￥{{(food.totalprice * food.count).toFixed(2)}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food"></cartcontrol>
                                    </div>
                                </div>
                                <div style="overflow: hidden" v-else>
                                    <span class="name">{{food.name}}</span>
                                    <div class="price">
                                        <span>￥{{(food.price * food.count).toFixed(2)}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food" :selectFoods="selectFoods" @qkgwc="listToggle"></cartcontrol>
                                        <!-- <cartcontrol :food="food"></cartcontrol> -->
                                    </div>
                                </div>
                                <div class="foodother" v-if="food.jcai">
                                  <span v-if="food.kweiname">{{food.kweiname}}&nbsp;</span>
                                  <span v-for="x in food.jcai" v-if="x.count>0">
                                    {{x.name}}<span class="chenghao">×</span>{{x.count}}&nbsp;
                                  </span>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </transition>
        </div>
        <transition name="fade-backdrop">
            <div class="backdrop" v-show="showBackdrop" @click="hideBackdrop"></div>
        </transition>
    </div>
</template>


<script>
import cartcontrol from 'components/cartcontrol/cartcontrol'
import backdrop from 'components/backdrop/backdrop'
import BScroll from 'better-scroll'

export default {
  props: {
    selectFoods: {
      type: Array,
      default: []
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      balls: [{
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }],
      dropBalls: [],
      listShow: false
    }
  },
  created() {
    this.$root.eventHub.$on('cart.add', this.drop)
  },
  computed: {
    showBackdrop() {
      if (this.listShow && this.totalPrice) {
        return true
      }
      this.listShow = false
      return false
    },
    totalPrice() {
      let total = 0
      this.selectFoods.forEach((food) => {
        if (food.count) {
          total += food.price * food.count
        }
      })
      return total.toFixed(2)
    },
    totalCount() {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    leftAmount() {
      if (this.minPrice - this.totalPrice > 0 && totalPrice) {
        return true;
      }
      return false
    },
    payDesc() {
      let diff = this.minPrice - this.totalPrice
      if (!this.totalPrice) {
        return '选好了'
        // return `￥${this.totalPrice}起送`
      } else if (diff > 0) {
        // return `还差￥${diff}元`
        return '请点餐'
      } else {
        return '选好了'
      }
    }
  },
  methods: {
    drop(el) {
      for (let i = 0, l = this.balls.length; i < l; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    setEmpty() {
      var _this=this;
        layer.open({
            content: '确定清空购物车？',
            btn: ['清空', '取消'],
            yes: function(index) {
                layer.close(index);

        _this.selectFoods.forEach((food) => {
          food.count = 0
        })
        _this.listToggle()


            }
        });

      // var msg = "确认清空购物车?";
      // if (confirm(msg) == true) {
      //   this.selectFoods.forEach((food) => {
      //     food.count = 0
      //   })
      //   this.listToggle()
      // } else {
      //   return false;
      // }

    },
    hideBackdrop() {
      this.listShow = false
    },
    _initScroll() {
      this.foodlistScroll = new BScroll(this.$refs.foodlist, {
        click: true
      });
    },
    listToggle() {
      if (!this.selectFoods.length) {
        return
      }
      this.listShow = !this.listShow
      if (this.listShow) {
        this.$nextTick(() => {
          if (!this.foodlistScroll) {
            this._initScroll()
          } else {
            this.foodlistScroll.refresh()
          }
        })
      }
    },
    goPay() {
      if (this.totalPrice > this.minPrice) {
        var tid=this.tid;
        var newselectFoodsArr = [];
        this.selectFoods.forEach(function(element, index) {
          var newfoodObj = {};
          newfoodObj.meal_id = element.meal_id;
          newfoodObj.price = element.price;
          newfoodObj.count = element.count;
          newfoodObj.kwei = element.kwei
          newfoodObj.kweiname = element.kweiname

          // newfoodObj.parent_meal_id = element.parent_meal_id;
          newfoodObj.jcai = []
          if(element.jcai){
          element.jcai.forEach((jcaiitem, index) => {
            if (jcaiitem.count > 0) {
              let childnodejcaiitem = {}
              childnodejcaiitem.rel_meal_id = jcaiitem.rel_meal_id
              childnodejcaiitem.name = jcaiitem.name
              childnodejcaiitem.price = jcaiitem.price
              childnodejcaiitem.count = jcaiitem.count
              newfoodObj.jcai.push(childnodejcaiitem)
            }
          })
          }

          newselectFoodsArr.push(newfoodObj);
        });

          console.log(newselectFoodsArr)

        $.ajax({
          url: postData.url,
          data: {
            'mer_id': postData.mer_id,
            'store_id': postData.store_id,
            "selectFoodsPos": newselectFoodsArr
          },
          type: "post",
          success: function(json) {
            if (json.code == 1) {
              go_pay();
            }
            if (json.code == 0) {
              alert(json.msg);
            }
          }
        });

      }

    },
    beforeEnter(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          el.style.transform = `translate3d(0,${y}px,0)`
          let inner = el.querySelector('.inner-hook')
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    enter(el) {
      el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.querySelector('.inner-hook')
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
      })
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  components: {
    cartcontrol,
    backdrop
  }
}
</script>

<style lang="stylus" scoped>
.shopCart
  position fixed
  left 0
  bottom 0
  width 100%
  height 1.1rem
  z-index 50
  .content
    display flex
    background #222222
    .content-left
      display inline-block
      width 5.3rem
      height 1.1rem
      .logo-wrapper
        display inline-block
        vertical-align top
        position: relative
        height: 56px
        line-height: 56px
        border-radius: 50%
        width: 56px
        top: -10px
        background: #000
        margin:0 12px
        padding 6px
        box-sizing border-box
        text-align: center
        .badge
          position absolute
          top: 0;
          right 0
          background: rgb(240,20,20);
          color: white;
          width .4rem
          height .4rem
          line-height: 16px;
          font-size: 9px;
          box-shadow: 0px 4px 8px 0px rgba(0,0,0,0.4);
          font-weight: 700;
          border-radius: 16px;
          text-align center
          background-image url('../../images/cartup.png')
          background-size 100%
        .cartdown
          background-image url('../../images/cartdown.png')
          background-size 100%
        .logo
          width 100%
          height 100%
          background: #000
          border-radius: 50%
          font-size: 24px
          color: #80858a
          line-height: 44px
          font-weight: 700
          &.active
            background: #000;
            color: white;
      .price
        display inline-block
        vertical-align top;
        font-size .32rem
        padding-right .24rem
        box-sizing border-box
        color rgba(255,255,255,0.4)
        font-weight 700
        line-height .8rem
        margin-top .1rem
        .small
         font-size .24rem
         line-height: .36rem
        p
          line-height: .42rem
        p:nth-child(1)
          margin-top .04rem
        &.active
          color white
      .desc
        position relative
        display inline-block
        vertical-align top
        margin 12px 0 0 12px
        font-size 10px
        color rgba(255,255,255,0.4)
        font-weight 700
        line-height 24px
    .content-right
      display inline-block
      width 2.2rem
      font-size .32rem
      font-weight 700
      background #999
      color rgba(255,255,255,1)
      line-height 1.1rem
      text-align center
      &.enough
        background #ef7f2d
        color white
  .ball-container
    .ball
      position fixed
      left 32px
      bottom 22px
      z-index 200
      &.drop-enter,&.drop-enter-active
        transition all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
        .inner
          width 16px
          height 16px
          border-radius 50%
          background #005e32
          transition all 0.4s linear
  .shopcart-list
    position absolute
    top 0
    left 0
    width 100%
    background white
    transform translate3d(0,-100%,0)
    z-index -1
    &.transHeight-enter-active,&.transHeight-leave-active
      transition all 0.5s
    &.transHeight-enter,&.transHeight-leave-active
      transform translate3d(0,0,0)
    .list-header
      height 40px
      line-height 40px
      background #f3f5f7
      border-bottom 1px solid rgba(7,17,27,0.1)
      .title
        display inline-block
        font-size .28rem
        font-weight 200
        color rgb(7,17,27)
        padding-left .24rem
      .empty
        position absolute
        right 8px
        font-size .28rem
        color #005e32
        padding 0 .1rem
    .list-content
      padding-bottom .1rem
      max-height 217px
      overflow hidden
      .food
        position relative
        display block
        // height .96rem
        margin 0 .24rem
        border-bottom 1px solid rgba(7,17,27,0.1)
        .name
          float left
          width 3.46rem
          overflow hidden
          font-size .28rem
          color rgb(7,17,27)
          height .96rem
          line-height .96rem
          font-weight 700
        .price
          float left
          font-size .28rem
          font-weight 700
          color rgb(240,20,20)
          padding 0 .1rem 0 .1rem
          line-height .96rem
        .cartcontrol-wrapper
          float right
          font-size .28rem
          margin-top .12rem
.backdrop
  position fixed
  top 0
  bottom 0
  left 0
  right 0
  background rgba(7,17,27,0.6)
  backdrop-filter blur(10px)
  z-index 40
  &.fade-backdrop-enter-active,&.fade-backdrop-leave-active
    transition opacity 0.5s
  &.fade-backdrop-enter,&.fade-backdrop-leave-active
    opacity 0
.foodother
  font-size .26rem
  padding: 0 0 .1rem
  color #969696
.chenghao
  font-size: .3rem
  display: inline-block
  height: .3rem
  line-height: .3rem

</style>

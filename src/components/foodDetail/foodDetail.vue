<template lang="html">
    <transition name="move">
        <div v-if="!food.dgg" v-show="showDetail">
                            <div class="foodDetailTitle">商品详情
                        <div class="back" @click="showToggle()">
                        </div>
                    </div>
            <div class="detailWrapper" ref="detailWrapper" >
                <div class="foodDetail">
                     <!-- <div class="foodpicbox" style="width: 100%; height: 4.5rem; overflow: hidden"><img :src="food.image.image" class="foodpic" style="width:100%;"></div> -->
                     <div class="foodpicbox" style="width: 100%; height: 4.5rem; overflow: hidden"><img :src="food.images.source_image" class="foodpic" style="width:100%;"></div>
                  <div class="bgbox">
                    <div class="des">
                      <div class="title">{{food.name}}</div>
                        <div v-if="food.label" class="subname">{{food.label}}</div>
                        <div class="desc">
                            <span v-if="food.is_scoreRatio==1">满{{food.spendingCash}}凤凰会返现{{food.scoreRatio}}%</span>
                        </div>
                        <div class="price">
                            <span class="unit">会员价：&yen{{food.price}}</span>
                            <span class="old_price" v-show="food.old_price"><del>原价：￥{{food.old_price}}</del></span>
                        </div>
                        <div class="shopCart">
                            <transition name="fade">
                                <div v-if="food.count_num==='0'">
                                    <p class="foodsqin">卖光啦！</p>
                                </div>
                                <div v-else class="text" @click="addCart($event)" v-show="!food.count">来一份</div>
                            </transition>
                        </div>
                        <cartcontrol v-show="food.count" :food="food"></cartcontrol>
                    </div>
                    <div class="divider"></div>
                    <div class="desc">
                        <div class="title">介绍</div>
                        <div class="content" v-html="food.des"></div>
                    </div></div>
                    <div style="height: 50px">&nbsp</div>
                </div>
                <div class="zhizhao">&nbsp</div>
            </div>
        </div>
        <div v-else v-show="showDetail">
                            <div class="foodDetailTitle">商品详情
                        <div class="back" @click="showToggle()">
                        </div>
                    </div>

            <div class="detailWrapper" ref="detailWrapper">
                <div class="foodDetail">
                  <!-- <div class="foodpicbox" style="width: 100%; height: 4.5rem; overflow: hidden; margin-bottom:1rem; padding-bottom:.4rem"><img :src="food.image.image" class="foodpic" style="width:100%;"></div> -->
                  <div class="foodpicbox" style="width: 100%; height: 4.5rem; overflow: hidden; margin-bottom:1rem; padding-bottom:.4rem"><img :src="food.images.source_image" class="foodpic" style="width:100%;"></div>
                    <div class="desc2" :class="{desc2show:Descshow}">
                        <div class="title">介绍</div>
                        <div class="content" v-html="food.des"></div>
                        <div v-show="Descshow" class="btnsj" :class="{btnsjon:Descshow}" @click="descShow()" style="position: absolute; bottom: .2rem; right: 0;">&nbsp;</div>
                        <div class="btnsj" :class="{btnsjon:Descshow}" @click="descShow()" style="position: absolute; top:0.02rem; right: 0;">&nbsp;</div>
                        <!-- <div class="btnsj" @click="descShow()">&nbsp;</div> -->
                    </div>
<div>
    <childthis :childfood="food" :selectFoods="selectFoods" :food="food" @dggchange="refreshScroll"></childthis>
</div>
                    <div v-for="(childfood,index) in food.dggcontent">
                        <childFood :childfood="childfood" :selectFoods="selectFoods" :food="food" :index="index" @dggchange="refreshScroll"></childFood>
                    </div>
                    <div class="divider"></div>

                    <div class="tuijian">
                      <div v-if="food.tjtangpins" class="title"><span class="big">一</span><span class="log">&nbsp;</span>推荐汤品&nbsp;&nbsp;<span class="big">一</span></div>
                      <div v-if="food.tjtangpins" v-for="tjtangpin in food.tjtangpins">
                          <div class="tuijianbox">
                              <span class="name"><span class="circlebox">&nbsp;</span>&nbsp;{{tjtangpin.name}}</span>
                              <div class="cartcontrol-wrapper">
                                  <cartcontrol :food="tjtangpin"></cartcontrol>
                              </div>
                              <div class="price">
                                  <span>￥{{tjtangpin.price*1}}</span>
                              </div>
                          </div>
                      </div>
                    </div>
                    <div class="divider"></div>

                    <div class="tuijian">
                      <div v-if="food.tjtianpins" class="title"><span class="big">一</span><span class="log">&nbsp;</span>推荐甜品&nbsp;&nbsp;<span class="big">一</span></div>
                      <div v-if="food.tjtianpins" v-for="tjtianpin in food.tjtianpins">
                          <div class="tuijianbox">
                              <span class="name"><span class="circlebox">&nbsp;</span>&nbsp;{{tjtianpin.name}}</span>
                              <div class="cartcontrol-wrapper">
                                  <cartcontrol :food="tjtianpin"></cartcontrol>
                              </div>
                              <div class="price">
                                  <span>￥{{tjtianpin.price*1}}</span>
                              </div>
                          </div>
                      </div>
                    </div>
                    <div class="divider"></div>

                    <div class="tuijian">
                      <div v-if="food.tjgongfuchas" class="title"><span class="big">一</span><span class="log">&nbsp;</span>推荐功夫茶(每泡/限6人内)&nbsp;&nbsp;<span class="big">一</span></div>
                      <div v-if="food.tjgongfuchas" v-for="tjgongfucha in food.tjgongfuchas">
                          <div class="tuijianbox">
                              <span class="name"><span class="circlebox">&nbsp;</span>&nbsp;{{tjgongfucha.name}}</span>
                              <div class="cartcontrol-wrapper">
                                  <cartcontrol :food="tjgongfucha"></cartcontrol>
                              </div>
                              <div class="price">
                                  <span>￥{{tjgongfucha.price*1}}</span>
                              </div>
                          </div>
                      </div>
                    </div>
                    <!-- <div class="des">
                        <div class="title">{{food.name}}</div>
                        <div class="desc">
                            <span>满30凤凰会返现5%</span>
                        </div>
                        <div class="price">
                            <span class="unit">会员价：&yen{{food.price}}</span>
                            <span class="old_price" v-show="food.old_price"><del>原价：￥{{food.old_price}}</del></span>
                        </div>
                        <div class="shopCart">
                            <transition name="fade">
                                <div v-if="food.count_num==='0'">
                                    <p class="foodsqin">售罄</p>
                                </div>
                                <div v-else class="text" @click="addCart($event)" v-show="!food.count">来一份</div>
                            </transition>
                        </div>
                        <cartcontrol v-show="food.count" :food="food"></cartcontrol>
                    </div> -->
                    <div class="divider"></div>
                    <div style="height: 10rem">&nbsp</div>
                </div>
                <div class="zhizhao">&nbsp</div>
            </div>
        </div>
    </transition>
</template>


<script>
// import '../../filter/time.js'
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import jcaicontrol from 'components/cartcontrol/jcaicontrol'
import childFood from 'components/foodDetail/childFood'
import childthis from 'components/foodDetail/childthis'


export default {
  components: {
    cartcontrol,
    jcaicontrol,
    childFood,
    childthis
  },
  props: {
    goods: Array,
    food: Object,
    selectFoods: {
      type: Array,
      default: []
    }
  },
  data() {
    return {
      showDetail: false,
      evelflag: true,
      descshow: false,
    }
  },
  computed: {
    Descshow: function() {
      return this.food.jsdescshow
    },

  },
  methods: {
    zoom(obj, width, height) {
      var img = new Image();
      img.src = obj.src;
      var scale = Math.max(width / img.width, height / img.height);
      var newWidth = img.width * scale;
      var newHeight = img.height * scale;
      var div = obj.parentNode;
      obj.width = newWidth;
      obj.height = newHeight;
      div.style.width = width + "px";
      div.style.height = height + "px";
      div.style.overflow = "hidden";
      obj.style.marginLeft = (width - newWidth) / 2 + "px";
      obj.style.marginTop = (height - newHeight) / 2 + "px";

    },
    showToggle() {
      this.showDetail = !this.showDetail
      if (this.showDetail) {
        this.$nextTick(() => {
          // this.detailWrapper = false
          this._initScroll()
          this.detailWrapper.scrollTo(0, 0)
        })
      }
    },
    refreshScroll() {
      if (this.showDetail) {
        this.$nextTick(() => {
          this._initScroll()
        })
      }
    },
    _initScroll() {
      if (!this.detailWrapper) {
        this.detailWrapper = new BScroll(this.$refs.detailWrapper, {
          click: true
        });
      } else {
        this.detailWrapper.refresh()
      }
    },
    addCart(event) {
      if (!event._constructed) {
        return
      }
      this.$set(this.food, 'count', 1)
      this.$root.eventHub.$emit('cart.add', event.target)
    },
    descShow() {
      this.food.jsdescshow = !(this.food.jsdescshow)
    }

  }
}
</script>
<style lang="stylus" scoped>
.detailWrapper
  overflow-y scroll
  -webkit-overflow-scrolling touch
  position fixed
  left 0
  top 0
  bottom 1.1rem
  width 100%
  padding-top .8rem
  transition all 0.4s ease
  &.move-enter-avtive,&.move-leave-active{
    transform translate3d(0,0,0)
  }
  &.move-enter,&.move-leave-active{
    transform translate3d(100%,0,0)
  }
.foodDetail
  position relative
  background #fff
  -webkit-overflow-scrolling touch
  z-index 50
  min-height: 13.34rem
  // .foodDetailTitle
  //   width 100%
  //   height .8rem
  //   background-image url('../../images/foodDetailTitle.jpg')
  //   background-size 100%
  //   line-height .8rem
  //   text-align center
  //   color white
  //   position absolute
  //   top: 0
  //   left:0
  // .back
  //   width .8rem
  //   height .8rem
  //   position absolute
  //   color white
  //   top 0
  //   right 0
  //   font-size 20px
  //   background-image url('../../images/back.png')
  //   background-size 100%
  .bgbox
    width 96%
    border 0.02rem solid #005e32
    border-radius: 0.2rem
    margin 0.5rem 2%
  .des
    position relative
    box-sizing border-box
    width 100%
    padding .26rem
    // border 0.02rem solid #005e32
    // border-radius: 0.2rem
    // margin 0.5rem 2%
    .title
      font-size .3rem
      color #005e32
      line-height .5rem
      font-weight bold
    .desc
      display flex
      padding 0
      font-size .24rem
      color #93999f
      line-height .5rem
      span:last-child
        // padding-left 12px
    .price
      display block
      padding-top .08rem
      font-size .28rem
      color #005e32
      line-height .5rem
      .unit
        // font-size 10px
        // font-weight normal
        display block
        line-height .4rem
      .old_price
        font-size .24rem
        color #999999
        line-height .5rem
    .shopCart
      position absolute
      right .46rem
      top .8rem
      text-align center
      z-index 2
      .foodsqin
              color red
      .text
        width 1.7rem
        height .6rem
        box-sizing border-box
        height 100%
        line-height .5rem
        color #005e32
        font-size .28rem
        border-radius .3rem
        background white
        border 1px solid #005e32
        &.fade-enter-active,&.fade-leave-active{
          transition opacity .2s
        }
        &.fade-enter,&.fade-leave-active{
          opacity 0
        }
    .cartcontrol
      font-size .30rem
      width 2.2rem
      height .6rem
      position absolute
      right .26rem
      top .8rem
      text-align center
  .desc
    padding 0 .26rem .32rem
    .title
      width .76rem
      font-size .26rem
      // color #07111b
      color #666
      float left
      line-height .38rem
    .content
      width 5.8rem
      float left
      font-size .26rem
      color #666
      line-height .38rem
    &:after
      content ''
      clear both
      display block
      height 0

  .evaluation
    padding 18px 0
    position relative
    .title
      padding-left 18px
      font-size: 14px
      font-weight 500
      color: #07111b
    .classify
      padding 18px 0
      margin 0 18px
      border-bottom 1px solid rgba(7,17,27,0.1)
      .item
        display inline-block
        font-size 12px
        padding 8px 12px
        line-height 16px
        background rgba(0,160,220,0.2)
        color rgb(77,85,95)
        margin-right 8px
        .count
          font-size 8px
          padding-left 2px
        &.active
          color white
          background rgb(0,169,220)
        &.bad
          background rgba(77,85,93,0.2)
        &.badActive
          background #4d555d
    .switch
      font-size 12px
      width 100%
      padding 12px 0 12px 18px
      color rgb(147,153,159)
      border-bottom 1px solid rgba(7,17,27,0.1)
      .icon-check_circle
        font-size 24px
        vertical-align middle
        &.on
          color #00c850
    .evel-list
      margin 0 18px
      .evel
        padding 16px 0
        border-bottom 1px solid rgba(7,17,27,0.1)
        .userdes
          display flex
          color rgb(147,153,159)
          font-size 10px
          line-height 12px
          .time
            flex 1
          .user
            flex 1
            text-align right
            .avatar
              img
                padding-left 6px
                border-radius 50%
        .content
          padding-top 6px
          .icon
            font-size 12px
            line-height 24px
            &.icon-thumb_up
              color rgb(0,160,220)
            &.icon-thumb_down
              color rgb(147,153,159)
          .text
            font-size 12px
            color rgb(7,17,27)
            line-height 16px
            padding-left 4px

.divider
  border none
  height 0
  margin 0 .26rem
  width auto
.zhizhao
  position fixed
  width 100%
  height 100%
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
.desc2
  width 6.62rem
  height .4rem
  position absolute
  overflow hidden
  top 4.5rem
  background-color white
  border-radius .2rem
  left .26rem
  padding .2rem
  z-index 99
  box-shadow 0.08rem 0.08rem 0.08rem rgba(0,0,0,.6);
  .title
    width .76rem
    font-size .26rem
    color #666666
    float left
    line-height .5rem
    background-color white

  .content
    width 5rem
    float left
    font-size .26rem
    color #666666
    line-height .5rem
    background-color white

  &:after
    content ''
    clear both
    display block
    height 0
.desc2show
  overflow visible
  height auto
.btnsj
  width: 1rem
  height: .8rem
  background-image url('../../images/btnsj_down.png')
  background-size 100%
  float right
.btnsjon
  background-image url('../../images/btnsj_up.png')
.tuijian
  position relative
  display block
  // height .96rem
  margin 0 .24rem
  .log
    width .32rem
    height .32rem
    display inline-block
    margin 0 .18rem
    background-image url('../../images/tuijianlog.jpg')
    background-size 100%
  .title
    text-align: center
    font-size .28rem
    color #eb344e
    height .32rem
    margin .4rem 0
    .big
      font-weight: 700
      line-height: 0
      padding: .16rem 0
      display: inline-block
  .tuijianbox
    // display: flex
    overflow: hidden
    padding-left .24rem
  .name
    font-weight: bold
    float left
    width 3.7rem
    // flex:5.5
    overflow hidden
    font-size .28rem
    color #005e32
    height .96rem
    line-height .96rem
    // padding: .48rem 0
    // white-space: nowrap
    position: relative
    text-indent: .1rem
    .circlebox
      display: inline-block
      width: .1rem
      height .1rem
      border-radius: .05rem
      background-color #005e32
      position: absolute
      left 0
      top .38rem


  .price
    flex 2
    float right
    font-size .28rem
    font-weight 700
    color #999999
    height: 0.96rem
    line-height 0.96rem
    // padding: .48rem .1rem

    white-space: nowrap
    span
     display inline-block
     width .5rem
     text-align left
     padding-right .2rem
  .cartcontrol-wrapper
    flex: 2.5
    float right
    font-size .28rem
    margin-top .14rem
    text-align: right
.foodDetailTitle
  width 100%
  height .8rem
  background-image url('../../images/foodDetailTitle.jpg')
  background-size 100%
  line-height .8rem
  text-align center
  color white
  position absolute
  top: 0
  left:0
  z-index: 1000
.back
  width 1.6rem
  height .8rem
  position absolute
  color white
  top 0
  right 0
  font-size 20px
  background-image url('../../images/back.png')
  background-size 100%
.subname
  font-size: .2rem;
  line-height: .36rem;
  white-space: nowrap;
  color #999

</style>

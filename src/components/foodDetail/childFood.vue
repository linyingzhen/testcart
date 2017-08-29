<template lang="html">
    <div class="dggbox">
        <div class="dgg" :class="{dgg2show:Descshow}">
            <div class="dgg-food">
              <span class="dgg-food-title">{{childfood.name}}<div v-if="childfood.label" class="subname">{{childfood.label}}</div></span>
              <!-- <span class="dgg-food-title"><span class="circlebox">&nbsp;</span>&nbsp;{{childfood.name}}<div v-if="food.label" class="subname">{{food.label}}</div></span> -->
                <!-- <span class="dgg-food-price">&yen {{childfood.price*1}}</span> -->
                <!-- <div class="dggbtnsj" @click="descShow()">&nbsp;</div> -->
                <!-- <div class="dggbtnsj" :class="{dggbtnsjon:Descshow}" @click="descShow()">&nbsp;</div> -->
                <div class="dggbtnsjzwei">
                <div class="dggbtnsj" :class="{dggbtnsjon:Descshow}" @click="descShow()" style="position: absolute; top:-.15rem; right: -.15rem;">&nbsp;</div>
                </div>
            </div>
            <div class="dgg-kwei" v-if="childfood.kwei.length>1">
                <span class="dgg-kwei-title">口味：</span>
                <ul class="dgg-kwei-list">
                    <li v-for="(kwei,kweiindex) in childfood.kwei" @click="selectKwei(kweiindex)" :class="{'on':flag==kweiindex }" :key="food.id">{{kwei.name}}</li>
                </ul>
            </div>
            <div class="dgg-jcai">
                <span class="dgg-jcai-title">配选：</span>
                <ul class="dgg-jcai-list">
                    <li v-for="jcai in childfood.jcai" v-if="jcai.count_num!=='0'">
                        <span>{{jcai.name}}</span>
                        <span>&yen{{jcai.price*1}}</span>
                        <span><jcaicontrol :childfood="childfood" :jcai="jcai"></jcaicontrol></span>
                    </li>
                </ul>
            </div>
            <div class="dgg-total">
                <span class="dgg-total-title">合计：&nbsp;&nbsp;&yen;{{ childfood.totalprice*1 }}</span>
                <!-- <span class="dgg-total-price">&yen;{{ childfood.totalprice*1 }}</span> -->
                <span class="dgg-total-price"></span>
                <div class="dgg-total-btn" @click="addCart($event)">来一份</div>
            </div>
        </div>
    </div>
</template>


<script>
// import '../../filter/time.js'
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import jcaicontrol from 'components/cartcontrol/jcaicontrol'


export default {
  components: {
    cartcontrol,
    jcaicontrol
  },
  props: {
    childfood: Object,
    index:Number,
    food: Object,
    selectFoods: {
      type: Array,
      default: []
    }
  },
  created() {
    this.childfood.selectkwei = this.childfood.kwei[0].id
    this.childfood.selectkweiname = this.childfood.kwei[0].name
    this.flag = 0
  },
  updated(){
      let index=this.flag
      this.childfood.selectkwei = this.childfood.kwei[index].id
      this.childfood.selectkweiname = this.childfood.kwei[index].name
  },
  data() {
    return {
      showDetail: false,
      evelflag: true,
      descshow: false,
      flag: 0
    }
  },
  computed: {
    Descshow: function() {
      return this.childfood.descshow
    },

  },
  methods: {
    selectKwei(index) {
      this.childfood.selectkwei = this.childfood.kwei[index].id
      this.childfood.selectkweiname = this.childfood.kwei[index].name
      this.flag = index;
    },
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
      this.$set(this.childfood, 'count', 1)
      let childnode = {}
      childnode.name = this.childfood.name
      childnode.kwei = this.childfood.selectkwei
      childnode.kweiname = this.childfood.selectkweiname
      childnode.meal_id = this.childfood.meal_id
      childnode.jcai = []
      var jcaitotal = 0;
      this.childfood.jcai.forEach((jcaiitem, index) => {
        if (jcaiitem.count > 0) {
          jcaitotal += parseFloat(jcaiitem.price) * jcaiitem.count
          let childnodejcaiitem = {}
          childnodejcaiitem.rel_meal_id = jcaiitem.rel_meal_id
          childnodejcaiitem.name = jcaiitem.name
          childnodejcaiitem.price = jcaiitem.price
          childnodejcaiitem.count = jcaiitem.count
          childnode.jcai.push(childnodejcaiitem)
          jcaiitem.count = 0

        }
      })
      this.childfood.totalprice = this.childfood.price
      childnode.price = parseFloat(this.childfood.price) + jcaitotal


      if (!this.food.childrens) {
        Vue.set(this.food, 'childrens', [])
      }
      childnode.count = 1
      // console.log(childnode)
      this.food.childrens.push(childnode)
      // console.log(this.food.childrens)
      this.$root.eventHub.$emit('cart.add', event.target)

    },
    descShow() {
      this.childfood.descshow = !(this.childfood.descshow)
      this.$emit('dggchange');
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
  padding-top .67rem
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
  .foodDetailTitle
    width 100%
    height .8rem
    background-image url('../../images/foodDetailTitle.jpg')
    background-size 100%
    line-height .8rem
    text-align center
    color white
  .back
    width .36rem
    height .36rem
    position absolute
    color white
    top .22rem
    right .26rem
    font-size 20px
    background-image url('../../images/back.png')
    background-size 100%
  .des
    position relative
    box-sizing border-box
    width 100%
    padding .26rem
    .title
      font-size .34rem
      color #333333
      line-height .5rem
    .desc
      display flex
      padding 0
      font-size .24rem
      color #f37916
      line-height .5rem
      span:last-child
        // padding-left 12px
    .price
      display block
      padding-top .20rem
      font-size .32rem
      color #005e32
      line-height .5rem
      .unit
        // font-size 10px
        // font-weight normal
        display block
      .old_price
        font-size .24rem
        color #999999
        line-height .5rem
    .shopCart
      position absolute
      right .26rem
      bottom .42rem
      text-align center
      z-index 2
      .foodsqin
              color red
      .text
        width 2rem
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
      width 2rem
      height .6rem
      position absolute
      right .26rem
      bottom .42rem
      text-align center
  .desc
    padding .32rem .26rem
    .title
      width .76rem
      font-size .26rem
      color #07111b
      float left
      line-height .38rem
    .content
      width 6.2rem
      float left
      font-size .26rem
      color rgb(77,85,93)
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
  border-bottom none
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
  width 6.58rem
  height .4rem
  position absolute
  overflow hidden
  top 4.9rem
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
    width 4.5rem
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
.dggbox
  // padding-top .5rem
  .dgg
    margin .5rem .26rem
    width 6.78rem
    padding: .1rem
    border:.02rem solid #005e32
    border-radius: .2rem
    line-height: .5rem
    color #333
    font-size: .28rem
    height: .4rem
    overflow: hidden
    // display: flex
    .dgg-food
      color: #005e32
      display: flex
      .dgg-food-title
        flex: 9
        line-height: .5rem
        // padding: .25rem 0
        position relative
        // text-indent .1rem
        float: left
        font-size: .3rem
        font-weight: bold
        .circlebox
          display: inline-block
          width: .1rem
          height .1rem
          border-radius: .05rem
          background-color #005e32
          position absolute
          left 0
          top .17rem
      .dgg-food-price
        padding-right .5rem
        text-align: right
        flex: 2
        color: #999999
    .dgg-kwei
      overflow: hidden
      margin .3rem auto
      .dgg-kwei-title
        display: inline-block
        float: left
        line-height: 0
        padding: .32rem 0
      .dgg-kwei-list
        display: inline-block
        float: left
        li
          float: left
          width 1.3rem
          line-height: 0
          padding .3rem 0
          border-radius: .3rem
          border 0.02rem solid #999999
          text-align: center
          margin-right .12rem
          &.on
           background #005e32
           color white
           border-color #005e32
          &:last-child
            margin-right 0
    .dgg-jcai
      overflow: hidden
      margin .3rem auto
      .dgg-jcai-title
        display: inline-block
        float: left
        line-height: .72rem
      .dgg-jcai-list
        display: inline-block
        float: left
        width: 5.9rem
        li
          display: flex
          line-height: .72rem
          span:nth-child(1)
            flex: 5.5
            overflow: hidden
            height: .72rem
          span:nth-child(2)
            text-indent:.06rem
            text-align: left
            flex:2
          :last-child
            flex:4
            position: relative
            .cartcontrol
              position: absolute
              right: 0
              top:0.01rem
    .dgg-total
      display: flex
      color #005e32
      .dgg-total-title
        font-weight: bold
        font-size: .28rem
        flex 6
        padding: .4rem 0 .24rem 0
        line-height: 0
      .dgg-total-price
        font-weight: bold
        font-size: .28rem
        flex 1
        padding: .32rem 0
        line-height: 0

      .dgg-total-btn
        font-size: .28rem
        flex 2.7
        line-height 0
        padding: .3rem 0
        border 0.02rem solid #005e32
        border-radius .3rem
        text-align center
  .dgg2show
    overflow visible
    height auto
.dggbtnsjzwei
  width: .8rem
  height: .5rem
  float right
  position: relative
.dggbtnsj
  width: 1rem
  height: .8rem
  background-image url('../../images/btnsj_down.png')
  background-size 100%
  float right
.dggbtnsjon
  background-image url('../../images/btnsj_up.png')
.subname
  font-size: .26rem
  margin-top .12rem
  line-height: .24rem
  white-space: nowrap
  color #999
  // text-indent 0.17rem
</style>

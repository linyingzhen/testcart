<template lang="html">

  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
     <!--  <li v-for="(item,index) in goods" @click="menuClick(index,$event)" :class="index==menuCurrentIndex?'menu-item-selected':'menu-item'" @click="selectStyle (item, $index) " :class="{'menu-item-selected':item.active,'menu-item':!item.active}"> -->
        <!-- <li v-for="(item,index) in goods" @click="menuClick(index,$event)" @click="selectStyle (item, $index) " :class="{'menu-item-selected':item.active,'menu-item':!item.active}"> -->
        <li v-for="(item,index) in goods" v-show="item.is_hide==='0'" @click="menuClick(index,$event)" :class="index==menuCurrentIndex?'menu-item-selected':'menu-item'">
          <span class="text" v-html="item.sort_name">
            <!-- <iconMap v-show="item.type>0" :iconType="item.type"></iconMap> -->
            <!-- {{item.sort_name}} -->
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" id="wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" v-show="item.is_hide==='0'" class="food-list food-list-hook">
          <h1>{{item.sort_name | filterBr}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item" @click="goDetail(food)">
              <div class="icon">
                <!-- 旧图地址 -->
                <!-- <img width="57" height="57" :src="food.image.s_image"/> -->
                <!-- 新图地址 -->
                <img width="57" height="57" :src="food.images.tumb_image"/>
              </div>
              <div class="content">
                <h2>{{food.name}}</h2>
                <p class="subname" v-show="food.subname">{{food.subname}}</p>
                <!-- <div class="sell-info">
                  <span class="sellCount">月售{{food.sellCount}}份</span>
                  <span class="rating">好评率{{food.rating}}%</span>
                </div> -->
                <div class="price">
                  <span class="newPrice"><span class="unit">￥</span>{{food.price}}</span>
                  <span v-show="food.old_price" class="old_price">￥{{food.old_price}}</span>
                </div>
                <div v-if="food.count_num>0" style="position: absolute; bottom:0.44rem;right: 1.7rem;color:red; font-size: .24rem">剩余{{food.count_num}}</div>
                <div v-if="food.count_num==='0'" class="cartcontrol-wrapper">
  <p class="foodsqin">卖光啦!</p>
</div>
                <div v-else class="cartcontrol-wrapper">
                  <cartcontrol v-if="!food.dgg" :food="food"></cartcontrol>
                  <a v-else class="btn-pc">进入选项</a>
                </div>

              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopCart :deliveryPrice="Number(seller.deliveryPrice)" :minPrice = "Number(seller.minPrice)" :selectFoods="selectFoods"></shopCart>
    <foodDetail :food="selectedFood" v-if="selectedFood" ref="myFood" :selectFoods="selectFoods"></foodDetail>
  </div>

</template>

<script>
// import iconMap from 'components/iconMap/iconMap'
import BScroll from 'better-scroll'
import shopCart from 'components/shopCart/shopCart'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import foodDetail from 'components/foodDetail/foodDetail'
// import axios from 'axios'
import Vue from 'vue'

const ERR_OK = 0
const eventHub = new Vue()
export default {
  props: {
    seller: Object
  },
  created() {

    this.goods = foodsData.goods;
    this.$nextTick(() => {
      this._initScroll(); // 初始化scroll
      this._calculateHeight(); // 初始化列表高度列表
    })


    // axios.get('static/data.json').then((res) => {
    //   this.goods = res.data.goods
    //   this.$nextTick(() => {
    //     this._initScroll(); // 初始化scroll
    //     this._calculateHeight(); // 初始化列表高度列表
    //   })
    // });


  },
  mounted() {
    if(foodsData.seller.food_id){
      let foodishas=false;
      this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if(food.meal_id==foodsData.seller.food_id){
              foodishas=true;
              if(food.count_num!=='0'){
                this.goDetail(food);
              } else {
                layer.open({
                    content: '对不起，您扫描的二维码所对应的商品卖完了或者已经下架。请继续选购其它商品，谢谢！',
                    btn: ['确定'],
                    yes: function(index) {
                        layer.close(index);
                    }
                });
              }
            }
          });

      });
      if(!foodishas){
        layer.open({
            content: '对不起，您扫描的二维码所对应的商品卖完了或者已经下架。请继续选购其它商品，谢谢！',
            btn: ['确定'],
            yes: function(index) {
                layer.close(index);
            }
        });
      }
    }
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      foodsScrollY: 0,
      selectedFood: '',
      active: false,
      isclick: true,
    }
  },
  filters: {
    filterBr(value){
      return value.replace(/<\/?[^>]*>/g,'');
    }
  },
  computed: {
    menuCurrentIndex() {
      for (let i = 0, l = this.listHeight.length; i < l; i++) {
        let topHeight = this.listHeight[i]
        let bottomHeight = this.listHeight[i + 1]
        if (!bottomHeight || (this.foodsScrollY >= topHeight && this.foodsScrollY < bottomHeight)) {
          return i
        }
      }
      return 0
    },
    selectFoods() {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
          if (food.dgg && food.childrens.length > 0) {
            food.childrens.forEach((child) => {
              if (child.count) {
                foods.push(child)
              }

            })
          }

          if (food.tjtangpins && food.tjtangpins.length > 0) {
            food.tjtangpins.forEach((tjtangpin) => {
              if (tjtangpin.count) {
                foods.push(tjtangpin)
              }

            })
          }

          if (food.tjtianpins && food.tjtianpins.length > 0) {
            food.tjtianpins.forEach((tjtianpin) => {
              if (tjtianpin.count) {
                foods.push(tjtianpin)
              }

            })
          }

          if (food.tjgongfuchas && food.tjgongfuchas.length > 0) {
            food.tjgongfuchas.forEach((tjgongfucha) => {
              if (tjgongfucha.count) {
                foods.push(tjgongfucha)
              }

            })
          }

        })
      })

      return foods
    }
  },
  methods: {
    _initScroll() {
      this.menuWrapper = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 1
      });
      // 监控滚动事件
      this.foodsScroll.on('scroll', (pos) => {
        this.isclick = false
        this.foodsScrollY = Math.abs(Math.round(pos.y))
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollStart', (pos) => {
        this.isclick = true
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollCancel', (pos) => {
        this.$nextTick(() => {
          this.isclick = true
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollEnd', (pos) => {
        this.isclick = false
        setTimeout(function() {
          this.isclick = true
        }, 1000)
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });

    },
    _calculateHeight() {
      let foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0, l = foodList.length; i < l; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    menuClick(index, event) {
      if (!event._constructed) {
        return
      }
      this.foodsScroll.scrollTo(0, -this.listHeight[index], 300)
    },
    goDetail(food) {
      if (this.isclick && food.count_num!=='0' && food.store_id !='34') {
        this.selectedFood = food
        if (!this.selectedFood.jsdescshow) {
          Vue.set(this.selectedFood, 'jsdescshow', false)
        }
        this.$nextTick(() => {
          this.$refs.myFood.showToggle()
        })
      }
    },
    selectStyle(item, index) {　　　　　　
      this.$nextTick(function() {　　　　　　　　
        this.goods.forEach(function(item) {　　　　　　　　　　
          Vue.set(item, 'active', false);　　　　　　　　
        });　　　　　　　　
        Vue.set(item, 'active', true);　　　　　　
      });　　　　
    }
    // listCountNum(foods){
    //   let count=0;
    //   foods.forEach((food)=>{
    //      count+= food.count
    //   })
    //   return count;
    // },
  },
  components: {
    // iconMap,
    shopCart,
    cartcontrol,
    foodDetail
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
  .goods
    display flex
    position absolute
    top 0
    bottom 1.1rem
    width 100%
    overflow hidden
    .menu-wrapper
      width 1.8rem
      background #f2f2f2
      margin-top: 0;
      .menu-item-selected
        color #005e32
        background white
        font-weight bold
        margin-top -1px
        padding 0 .12rem 0 .12rem
        .text
          font-weight bold
          // font-size .28rem
      .menu-item,.menu-item-selected
        position relative
        display table
        font-size .26rem
        height 0.93rem
        line-height .28rem
        width 1.58rem
        padding 0 .13rem
        text-align: center
        &:last-child:after
          content none
      .menu-item:after
          position: absolute
          content: ''
          left: .24rem
          width: 56px
          bottom: 0
        .text
          display table-cell
          vertical-align middle
          white-space normal
          line-height .32rem
          // .iconMap
          //   vertical-align middle
    .foods-wrapper
      width 5.7rem
      margin-top: 0;
      .food-list
        h1
          height .6rem
          line-height .6rem
          margin-left .2rem
          font-size .24rem
          color #055c39
          background #fff
          border-bottom 1px dashed #ccc
      .food-item
        position relative
        display block
        margin: 0 .2rem;
        padding: .2rem 0;
        border-bottom 1px solid rgba(7,17,27,0.1)
        overflow hidden
        .icon
          float left
          width 1.3rem
          display inline-block
          img
            width 1.3rem
            height 1.3rem
            border-radius 0.07rem
        &:last-child
          border-bottom none
        .content
          display inline-block
          font-size .30rem
          width 3.8rem
          padding-left .2rem
          h2
            overflow hidden
            // white-space: nowrap
            margin 0 0 .16rem 0
            font-size .28rem
            height .32rem
            line-height .36rem
            color rgb(7,17,27)
          .sell-info,.subname
            font-size .2rem
            color rgb(147,153,159)
            line-height .2rem
            .sellCount
              margin-right .08rem
          .subname
            font-size .2rem
            margin-bottom .16rem
            line-height .24rem
            white-space nowrap
          .price
            font-size .2rem
            font-weight 700
            line-height .32rem
            .newPrice
              display block
              font-size .28rem
              color rgb(240,20,20)
              .unit
                font-size .2rem
                font-weight normal
            .old_price
              text-decoration line-through
              color rgb(147,153,159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom .18rem
            z-index 20
            .foodsqin
              color red
              padding: .2rem
              font-size: .28rem
  .btn-pc
    position: relative;
    float: right;
    border: 1px solid #085a36;
    padding: 5px 7px;
    font-size: 13px;
    color: #085a36;
    text-decoration: none;
    border-radius: 14px;


</style>

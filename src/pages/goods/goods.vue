<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <!--current-->
        <li v-for="(good, index) in goods" :key="index" class="menu-item"
            :class="{current:currentIndex===index}" @click="clickMenu(index)">
          <span class="text border-1px">
            <span class="icon guarantee"></span>
            {{good.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="(good, index) in goods" :key="index" class="food-list food-list-hook">
          <h1 class="title">{{good.name}}</h1>
          <ul>
            <li v-for="(food, index) in good.foods" :key="index" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon"/>
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.name}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"/>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart />
  </div>
</template>

<script>
  import {mapState} from "vuex"
  import BScroll from "better-scroll"
  import cartcontrol from "../../components/cartcontrol/cartcontrol.vue"
  import shopcart from "../../components/shopcart/shopcart.vue"
  export default {
    data(){
      return {
        tops: [],
        scrollY: 0
      }
    },
    computed: {
      currentIndex(){
        let {tops, scrollY} = this
        return tops.findIndex((top, index) => scrollY >= top && scrollY < tops[index + 1])
      },
      ...mapState(["goods"])
    },
    mounted(){
      this.$store.dispatch("requestGoods", () => {
        this.$nextTick(() => {
          this._initScroll()
          this._initTops()
        })
      })
    },
    methods: {
      _initScroll(){
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true,
          probeType: 2
        })
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        })
        this.foodsScroll.on("scroll",(event)=>{
          this.scrollY = Math.abs(event.y)
        })
        /*this.foodsScroll.on('scrollEnd', (event) => {
          // 获取滚动的y坐标
          console.log('scrollEnd', event.y)
          this.scrollY = Math.abs(event.y)
        })*/
      },
      _initTops(){
        let lis = this.$refs.foodsWrapper.getElementsByClassName("food-list")
        let tops = []
        let top = 0
        tops.push(top)
        /*Array.prototype.slice.call(lis).forEach( li =>{
         top += li.clientHeight
         console.log(top)
         tops.push(top)
         })*/

        for (let i = 0; i < lis.length; i++) {
          let li = lis[i];
          top += li.clientHeight
          tops.push(top)
        }
        this.tops = tops
        console.log(tops)
      },
      clickMenu(index){
        this.scrollY = this.tops[index]
        this.foodsScroll.scrollTo(0, -this.scrollY, 300)
      }
    },
    components:{
      cartcontrol,
      shopcart
    }

  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixins.styl"

  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background: #fff
          font-weight: 700
          .text
            border-none()
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            line-height: 12px
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              color: rgb(147, 153, 159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>


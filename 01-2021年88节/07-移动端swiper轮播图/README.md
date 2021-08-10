# 
# 概要

- `swiper` 功能强大，但是配置繁琐，尤其各种奇怪的 `bug` , 让人又爱又恨

- 一般简单页面推荐 `elementUi` 的 `carousel`, 移动端万不得已 再使用 `swiper`

# 1. 数据分组并快速搭建
## 快速搭建
```js
  import { Swiper, SwiperSlide } from "vue-awesome-swiper";
  import _ from 'lodash';
  import "swiper/dist/css/swiper.css";
```
```js
data(){
    return {
      swiperOptions: {
        loop: false,
        direction: "horizontal",
        effect:"slide",
        speed:800,
        spaceBetween: 15,
        allowTouchMove:true,
        autoplay: {
          delay: 5000,
          disableOnInteraction: false, //用户交互之后是否禁止，false不禁止
          // pauseOnMouseEnter:true, //鼠标移入之后是否展厅，true暂停
          // waitForTransition: true, // 过度结束后才开始计时 true
        },
        pagination: {
          el: '.swiper-pagination',
          bulletClass: 'my-butllet',
          bulletActiveClass: 'my-butllet-acitve',
        },
      },
    }
  },
```
```html
    <swiper
      ref="mySwiper"
      class="free-nuts-swiper"
      :options="swiperOptions"
    >
      <swiper-slide class="free-nuts-slider" v-for="(itemArr,index) in carouselArray" :key="index+'free-nuts-slider'">
        <div class="free-nuts-itme" v-for="(ele,idx) in itemArr"
        :key="idx+'free-nuts-itme'"
        >
          <div class="nuts-main-title">
            <span class="main-title-body">{{mainTitle(ele)}}</span>
            <div class="main-title-tag" v-if="titleTag(ele)">
              <span>{{titleTag(ele)}}</span>
            </div>
          </div>
        </div>
      </swiper-slide>
      <div v-show=" carouselArray.length>1" class="swiper-pagination" slot="pagination"></div>
    </swiper>
```
## 数据分组

```js
  import _ from 'lodash';
```
```js
computed:{
  carouselArray(){
    let arr = this.freeNuts && this.freeNuts.length>0 ? this.freeNuts : []
    return _.chunk(arr,4)
  },
},
```
## 样式修改
```vue
<style lang="less">
@import url('../common/common.less');
.free-nuts-wrap{
  width: 100%;
  height: auto;
  .padding-left(25);
  .padding-right(25);
  .free-nuts-swiper{
    width: 100%;
    .height(700);
    transform:translate3d(0,0,0);
    overflow:hidden;
    .swiper-wrapper{
      width: 100%;
      .height(660);
      .free-nuts-slider{
        width: 100%;
        height: 100%;
        transform:translate3d(0,0,0);
        .free-nuts-itme{
          float: left;
          .width(342);
          .height(322);
          background: #fff;
          &:nth-child(2n){
            margin-right: 0;
          }
        }
      }
    }
    .swiper-pagination {
      width: 100%;
      .height(8);
      bottom: 0;
      line-height: 0;
      z-index: 0;
      font-size: 0;
      .my-butllet {
        background: #fff;
        margin: 0 4.5px;
        display: inline-block;
        .width(20);
        .height(8);
      }
      .my-butllet-acitve {
        .width(50);
        .height(8);
        background: #F21717 !important;
      }
    }
  }
}
</style>
```

# 2. 分页器展示

- `pigination` 和 `swiper-slide` DOM 同级 CSS 同级

- `swiper-pagination` 分页器位置
 
- `my-butllet` 静态分页器样式

- `my-butllet-acitve` 分页器激活样式

- 添加配置
```js
   pagination: {
    el: '.swiper-pagination',
    bulletClass: 'my-butllet',
    bulletActiveClass: 'my-butllet-acitve',
  },
 
```

# 3. swiper轮播图掉帧的问题处理

```less
.free-nuts-swiper{
    //...
    transform:translate3d(0,0,0); //最外层 swiper 组件添加该属性
    overflow:hidden; //最外层 swiper 组件添加该属性
    .swiper-wrapper{
      //...
      .free-nuts-slider { 
        transform: translate3d(0, 0, 0);// 每一个 swiper-slide 组件添加该属性
        //...
        .free-nuts-itme {
          //...
        }
      }
    }
}
```

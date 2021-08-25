# 
# 1. 优惠券样式逻辑

* 优惠券分为四种：通用券，专用券、折扣（现金）券、不可用券
* 分别对应四种颜色 gold  green  red   gray
* 先写上基本的样式，在基本的样式上添加 .gold   .green   .red   .gray 四种不同的样式进行覆盖
* 使用一个方法，接受一个参数，根据这个参数来判断当前的优惠券的种类

# 2. 优惠券中提示对话框自定义样式 

【 原理：】
* 使用 element 的组件 `<el-tooltip ></el-tooltip >` 
* 使用`<div slot="content"></div>` 包裹提示的信息

```html
<el-tooltip 
  placement="bottom"
  v-if="businessLimit"
>
  <div slot="content">
    <a 
    class="popover-item" 
    target="_blank" 
    v-for="(itemChild,index) in cardInfo.activitySpRelationList"
    :href="getShopHref(itemChild)" 
    :key='index+"popover-item"'
    >
      <span :class="['tip-link',getShopHref(itemChild)?'has_link':'']">
        {{getItemName(itemChild, 2)}}{{index != cardInfo.activitySpRelationList.length-1?'、':''}}
      </span> 
    </a>
  </div>
  <div 
  :class="['cards-name-text',cardInfo.productBasicList.length && cardInfo.activitySpRelationList.length ? 'double_style':'']" 
  >
    <span class="cards-tag">限商家</span>
    <i class="icon-pull-down suffix" />
  </div>
</el-tooltip>
```
# 3. 触发优惠券操作的更新逻辑

* 在 `pages` 目标路径的 `index.vue` 文件中写一个 `refreshOrder` 的方法，该方法接受参数，并发起 `ajax` 的请求，之后更新 `index.vue `中的数据，由于该数据会 `props` 传递到子组件中，达到更新数据的目的

# 4. 优惠码功能实现的操作逻辑

```js
export default {
    methods:{
        refreshOrder(info, type) {
            this.info = info;
            if(type===3){
                const activityList = info.activityList || [];
                let activityListResult = [];
                activityList.forEach((item) => {
                    if (item.ticketIdList) {
                        activityListResult = activityListResult.concat(item.ticketIdList);
                    }
                });
                this.bindCouponId = activityListResult
            }
        },
    }
}
```
# 5. elementUI 勾选框下拉选框默认样式修改

```less
 .el-checkbox{
  .el-checkbox__input{
    &.is-focus{
      .el-checkbox__inner{
        border-color:#d8dce5 ;
      }
    }
    &.is-checked{
      .el-checkbox__inner{
        background-color:#ff9072 !important;
        border-color:#ff9072 ;
      }
    }
    &.is-disabled{
      .el-checkbox__inner{
         &::after{
           border-color: #fff;
         }
      }
    }
  }
  .el-checkbox__label{
    font-size: 12px;
    font-weight: 400;
    color: #666666;
  }
}

```

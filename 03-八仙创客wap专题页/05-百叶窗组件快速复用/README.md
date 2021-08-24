# 

# 1.实现原理
* `selectedIndex` 为选中百业窗的索引
* 百业窗氛围两个部分，顶部为固定高度的部分，底部为增长的部分
* 底部判断 `selectedIndex === index `高度增长，否则高度归零


# 2.动画效果
* 动画效果其实就是让下边部分的增长，但是这里不好控制 `height`, 因为很多时候高度都是自动适应的，所以这里最好控制` max-height `

* 添加动画效果的时候，记得添加 `opacity` `visibility` 这样会让消失出现呈现地更加平滑

* 这里百业窗中的样式中还有 小三角 的样式，仅供参考

```less
 .list_sub_info {
  width: 340px;
  max-height: 0px;
  background: #fefefd;
  border: 1px solid transparent;
  box-shadow: 0px 0px 16px 0px rgba(212, 217, 219, 0.33);
  border-radius: 4px;
  position: relative;
  margin-top: 9px;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  flex-wrap: wrap;
  visibility: hidden;
  opacity: 0;
  transition-property: max-height, border, visibility, opacity;
  transition-duration: 0.2s;
  transition-timing-function: linear;
  transition-delay: 0s;
  .sub_info_inner {
    padding: 10px 15px 10px;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    text-align: left;
    span {
      font-size: 13px;
      font-weight: 400;
      color: #333333;
      line-height: 16px;
    }
  }
  &.active {
    max-height: 120px;
    border: 1px solid #cad1da;
    visibility: visible;
    opacity: 1;
  }
  &::before {
    display: block;
    position: absolute;
    content: '';
    width: 8px;
    height: 8px;
    background: #fefefd;
    top: -5px;
    left: 30px;
    transform: rotateZ(-45deg);
    z-index: 1;
    border-top: 1px solid #cad1da;
    border-right: 1px solid #cad1da;
    border-left: none;
    border-bottom: none;
    border-radius: 2px;
  }
}
```
# 3.快速自定义

* `dom` `js` `css` 大部分都是可以快速复制的

* 更改的就只是布局，颜色背景，高度等等

# 4.源码

```vue
<template>
  <div class="maker_questions_wrap">
    <div class="maker_questions_title">常见问答</div>
    <ul class="maker_question_body">
      <li
        class="maker_question_list_item"
        v-for="(item, index) in quesList"
        :key="index + 'maker_question_list_item'"
        @click="activeAnswer(index)"
      >
        <div class="list_main_info">
          <div class="main_info_index">0{{ index + 1 }}</div>
          <div class="main_info_text">{{ item.title }}</div>
          <i
            :class="['icon-shouqi suffix_icon', index === activeIndex ? 'active' : '']"
          ></i>
        </div>
        <div :class="['list_sub_info', index === activeIndex ? 'active' : '']">
          <div class="sub_info_inner">
            <span v-for="(ele, idx) in item.descList" :key="idx + 'list_sub_info'">{{
              ele
            }}</span>
          </div>
        </div>
      </li>
    </ul>
    <div class="maker_question_more">
      <a :href="askUrl" target="_blank">
        <span>{{`咨询更多问题 >`}}</span>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    askUrl() {
      return this.$store.state.askUrl;
    }
  },
  data() {
    return {
      activeIndex: 0,
      quesList: [
        {
          title: '“八仙创客合伙人”可获得哪些收益？',
          descList: ['1、底价现金奖励;', '2、返税现金奖励;', '3、发展增值收益;']
        },
        {
          title: '我们具体该怎么建立起合作呢？',
          descList: [
            '1、与当地城市负责人沟通洽谈，确立合作意向成为合伙人；',
            '2、指派专业服务顾问进行一对一支撑，陪访客户谈判等；',
            '3、客户成交订单后获得佣金报酬。'
          ]
        },
        {
          title: '哪些方式可以申请成为“八仙创客合伙人”？',
          descList: [
            '方式一：直接在本页面提交申请；',
            '方式二：拨打客服电话023-88392535或者400-023-3766进行申请；',
            '方式三：通过微信小程序搜索“八仙创客”进行申请。'
          ]
        },
        {
          title: '“八仙创客”在为八戒财税提供了符合要求的 商户后，多久能拿到返佣呢？',
          descList: [
            '答：由八戒财税专业的强大售后团队，来为客户提供高质量的体系化售后服务。'
          ]
        }
      ]
    };
  },
  methods: {
    activeAnswer(index) {
      this.activeIndex = index;
    }
  }
};
</script>

<style lang='less' scoped>
.maker_questions_wrap {
  width: 100%;
  min-height: 300px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  overflow: hidden;
  background: #fff;
  position: relative;
  .maker_questions_title {
    min-width: 136px;
    height: auto;
    text-align: center;
    font-size: 23px;
    font-weight: bolder;
    color: #333;
    line-height: 24px;
    margin-top: 37px;
    margin-bottom: 37px;
    position: relative;
  }
  .maker_question_body {
    width: 100%;
    height: auto;
    padding-bottom: 30px;
    .maker_question_list_item {
      width: 100%;
      height: auto;
      padding: 0 17px;
      margin-bottom: 30px;
      .list_main_info {
        width: 100%;
        height: 26px;
        display: flex;
        flex-direction: row;
        justify-content: flex-start;
        align-items: center;
        position: relative;
        .main_info_index {
          width: 24px;
          height: 24px;
          background: #8993a7;
          border-radius: 2px;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 16px;
          font-weight: normal;
          color: #fefefd;
          margin-right: 10px;
        }
        .main_info_text {
          font-size: 15px;
          font-weight: normal;
          color: #333333;
          line-height: 15px;
          display: block;
          max-width: 265px;
          white-space: pre-wrap;
        }
        .suffix_icon {
          position: absolute;
          font-size: 22px;
          top: 50%;
          right: 0px;
          color: #999;
          transform: translateY(-50%);
          transition: transform 0.2s linear 0s;
          &.active {
            transform: translateY(-50%) rotate(180deg);
          }
        }
      }
      .list_sub_info {
        width: 340px;
        max-height: 0px;
        background: #fefefd;
        border: 1px solid transparent;
        box-shadow: 0px 0px 16px 0px rgba(212, 217, 219, 0.33);
        border-radius: 4px;
        position: relative;
        margin-top: 9px;
        display: flex;
        flex-direction: row;
        justify-content: flex-start;
        align-items: center;
        flex-wrap: wrap;
        visibility: hidden;
        opacity: 0;
        transition-property: max-height, border, visibility, opacity;
        transition-duration: 0.2s;
        transition-timing-function: linear;
        transition-delay: 0s;
        .sub_info_inner {
          padding: 10px 15px 10px;
          display: flex;
          flex-direction: column;
          justify-content: flex-start;
          align-items: flex-start;
          text-align: left;
          span {
            font-size: 13px;
            font-weight: 400;
            color: #333333;
            line-height: 16px;
          }
        }
        &.active {
          max-height: 120px;
          border: 1px solid #cad1da;
          visibility: visible;
          opacity: 1;
        }
        &::before {
          display: block;
          position: absolute;
          content: '';
          width: 8px;
          height: 8px;
          background: #fefefd;
          top: -5px;
          left: 30px;
          transform: rotateZ(-45deg);
          z-index: 1;
          border-top: 1px solid #cad1da;
          border-right: 1px solid #cad1da;
          border-left: none;
          border-bottom: none;
          border-radius: 2px;
        }
      }
    }
  }
  .maker_question_more {
    width: 100%;
    height: auto;
    padding-bottom: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    a {
      span {
        font-size: 14px;
        font-weight: 400;
        color: #ff6900;
      }
    }
  }
}
</style>

```

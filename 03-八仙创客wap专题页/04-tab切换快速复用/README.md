# 

# 1. 实现原理
* selectedIndex 保存为选中的值的索引
* activeClass 设置选中之后的样式
* :class=[ 'base_class', selectedIndex === index ? 'activeClass':'' ]

# 2. 动画效果
* 穿梭框的效果实现
```less
 .outer_tab_item {
      width: 171px;
      height: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 13px;
      font-weight: bold;
      color: #333333;
      position: relative;
      transition: color 0.2s linear 0s;
      &::before {
        width: 0px;
        height: 2px;
        background: #ff6900;
        position: absolute;
        display: block;
        content: '';
        bottom: 0px;
        transition: width 0.2s linear 0s;
      }
      &.left_tab {
        &::before {
          right: 0px;
        }
      }
      &.right_tab {
        &::before {
          left: 0px;
        }
      }
      &.active_tab {
        color: #ff6900;
        &::before {
          width: 171px;
        }
      }
    }
```
# 3. 快速自定义 
* 快速构造dom 元素
* 快速更改 css 样式
* 沿用js 逻辑

# 4. 源码

```vue
<template>
  <div class="maker_cooperation_wrap">
    <div class="maker_cooperation_title">八仙创客合作模式介绍</div>
    <div class="maker_outer_tab">
      <div
        :class="[
          'outer_tab_item',
          idx === 0 ? 'left_tab' : 'right_tab',
          idx === selectedIndex ? 'active_tab' : ''
        ]"
        v-for="(ele, idx) in cooperationList"
        :key="idx + 'outer_tab_item'"
        @click="tabSelect(idx)"
      >
        {{ ele.tabName }}
      </div>
    </div>
    <div
      class="maker_cooperation_content"
      v-for="(item, index) in cooperationList"
      :key="index + 'maker_cooperation_content'"
      v-show="selectedIndex === index"
    >
      <div :class="['maker_coop_flow', index === 1 ? 'second_flow' : '']">
        <span class="coop_flow_top">{{ item.topText }}</span>
        <span class="coop_flow_bottom">{{ item.bottomText }}</span>
        <div class="coop_flow_left">
          <div class="coop_flow_avator">
            <img :src="item.leftPicUrl" alt="八仙创客" class="flow_avator_bg" />
          </div>
          <div class="coop_flow_infos">
            <span class="main_info">{{ item.leftText[0] }}</span>
            <span class="sub_info">{{ item.leftText[1] }}</span>
          </div>
        </div>
        <div class="coop_flow_right">
          <div class="coop_flow_avator">
            <img :src="item.rightPicUrl" alt="八仙创客" class="flow_avator_bg" />
          </div>
          <div class="coop_flow_infos">
            <span class="main_info">{{ item.rightText[0] }}</span>
            <span class="sub_info">{{ item.rightText[1] }}</span>
          </div>
        </div>
        <div class="coop_center_list">
          <span
            class="center_list_item"
            v-for="(ele, idx) in item.centerList"
            :key="idx + 'center_list_item'"
            >{{ ele }}</span
          >
        </div>
      </div>
      <div :class="['maker_coop_file', index === 1 ? 'second_file' : '']">
        <div class="coop_title_tag">
          <div class="coop_title">{{ item.tabName }}</div>
          <div class="coop_tag">{{ item.tagName }}</div>
        </div>
        <span class="coop_desc">{{ item.tabContents[0] }}</span>
        <span class="coop_desc">{{ item.tabContents[1] }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedIndex: 0,
      cooperationList: [
        {
          tabName: '企业版',
          topText: '发掘合作用户，由八戒财税落地服务',
          bottomText: '满足客户需求，分享现金报酬',
          leftText: ['八仙创客企业版', '金牌/钻石合伙人'],
          leftPicUrl: require('./images/company_house.png'),
          rightText: ['八戒财税'],
          rightPicUrl: require('./images/bjcs_house.png'),
          centerList: [
            '· 产品底价代售',
            '· 营销培训支撑',
            '· 品牌服务赋能',
            '· 运营体系扶持'
          ],
          tagName: '限定行业内',
          tabContents: [
            '八戒财税为合伙人提供系统的产品和营销培训，必要的产品物料支持，体系化的运营扶持和完备的售后保障。满足特定条件还可优先定向推送本地化商机及共享政府资源。',
            '合伙人享底价来代售服务产品，和客户达成合作后由八戒财税提供服务，收完客户款项后获得现金报酬。'
          ],
          filePicUrl: require('./images/gray_file.png')
        },
        {
          tabName: '个人版',
          topText: '提供意向客户线索商机',
          bottomText: '转化客户成单 分享佣金返点',
          leftText: ['八戒财税'],
          leftPicUrl: require('./images/bjcs_house.png'),
          rightText: ['八仙创客合伙人', '个人合伙人'],
          rightPicUrl: require('./images/personal_avator.png'),
          centerList: ['渠道大额分润', '品牌体系支撑'],
          tagName: '限定行业外',
          tabContents: [
            '八戒财税为合伙人提供系统的产品和营销培训，必要的产品物料支持，体系化的运营扶持和完备的售后保障。满足特定条件还可定向推送本地化商机。',
            '合伙人提供意向客户商机，由八戒财税转化成单，收完客户款项后获得渠道返点。'
          ]
        }
      ]
    };
  },
  methods: {
    tabSelect(idx) {
      this.selectedIndex = idx;
    }
  }
};
</script>

<style lang='less' scoped>
.maker_cooperation_wrap {
  width: 100%;
  height: 598px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  overflow: hidden;
  position: relative;
  background: #fff;
  .maker_cooperation_title {
    width: 100%;
    text-align: center;
    font-size: 24px;
    font-weight: normal;
    color: #333333;
    line-height: 24px;
    margin-top: 70px;
  }
  .maker_outer_tab {
    width: 342px;
    height: 40px;
    background: #fff;
    margin-top: 30px;
    border-bottom: 1px solid #c4c6c7;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    .outer_tab_item {
      width: 171px;
      height: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 13px;
      font-weight: bold;
      color: #333333;
      position: relative;
      transition: color 0.2s linear 0s;
      &::before {
        width: 0px;
        height: 2px;
        background: #ff6900;
        position: absolute;
        display: block;
        content: '';
        bottom: 0px;
        transition: width 0.2s linear 0s;
      }
      &.left_tab {
        &::before {
          right: 0px;
        }
      }
      &.right_tab {
        &::before {
          left: 0px;
        }
      }
      &.active_tab {
        color: #ff6900;
        &::before {
          width: 171px;
        }
      }
    }
  }
  .maker_cooperation_content {
    width: 100%;
    height: 446px;
    background: #fff;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    .maker_coop_flow {
      width: 351px;
      height: 180px;
      background: url('./images/flow_arrow.png') no-repeat;
      background-size: 100%;
      margin-top: 27px;
      position: relative;
      &.second_flow {
        .coop_flow_left,
        .coop_flow_right {
          .coop_flow_avator {
            border: 1px solid #fc6f23;
            background: #fffaf5;
          }
          .coop_flow_infos {
            background: #fc6f23;
          }
        }
        .coop_flow_right {
          left: auto;
          right: 0;
          .coop_flow_avator {
            border: 1px solid #a2acbf;
            background: #f4f8ff;
          }
          .coop_flow_infos {
            background: #a2acbf;
          }
        }
        .coop_center_list {
          background: url('./images/direction_arrow.png') no-repeat;
          background-size: 100% 100%;
          transform: translate(-50%, -50%) rotate(180deg);
          .center_list_item {
            font-size: 12px;
            font-weight: normal;
            color: #fc6f23;
            line-height: 14px;
            text-align: left;
            transform: rotate(180deg);
          }
        }
      }
      .coop_flow_top,
      .coop_flow_bottom {
        display: inline-block;
        width: auto;
        white-space: nowrap;
        position: absolute;
        top: 0;
        left: 50%;
        transform: translateX(-50%);
        font-size: 12px;
        font-weight: normal;
        color: #222222;
      }
      .coop_flow_bottom {
        top: auto;
        bottom: 0;
      }

      .coop_flow_left,
      .coop_flow_right {
        width: 98px;
        height: 85px;
        position: absolute;
        left: 0;
        top: 46px;
        .coop_flow_avator {
          width: 100%;
          height: 57px;
          border: 1px solid #b7c2d6;
          background: #f4f8ff;
          border-radius: 5px 5px 0 0;
          border-bottom: none;
          display: flex;
          justify-content: center;
          align-items: center;
          .flow_avator_bg {
            width: 50px;
            height: 50px;
            display: block;
          }
        }
        .coop_flow_infos {
          width: 100%;
          height: 22.5px;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
          flex-wrap: nowrap;
          background: #8793a9;
          border-radius: 0px 0px 12px 12px;
          text-align: center;
          .main_info {
            font-size: 12px;
            font-weight: normal;
            color: #ffffff;
            transform: scale(0.9);
            line-height: 12px;
          }
          .sub_info {
            font-size: 12px;
            font-weight: normal;
            color: rgba(252, 253, 254, 0.5);
            transform: scale(0.7);
            line-height: 8px;
          }
        }
      }
      .coop_flow_right {
        left: auto;
        right: 0;
        .coop_flow_avator {
          border: 1px solid #fc6f23;
          background: #fffaf5;
        }
        .coop_flow_infos {
          background: #fc6f23;
        }
      }

      .coop_center_list {
        position: absolute;
        width: 133px;
        height: auto;
        padding: 16px 26px;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
        background: url('./images/direction_arrow.png') no-repeat;
        background-size: 100% 100%;
        .center_list_item {
          font-size: 12px;
          font-weight: normal;
          color: #fc6f23;
          line-height: 14px;
          text-align: left;
        }
      }
    }
    .maker_coop_file {
      width: 341px;
      height: 179px;
      background: #eee;
      margin-top: 21px;
      padding: 0 26px;
      background: url('./images/gray_file.png') no-repeat;
      background-size: 100% 100%;
      &.second_file {
        background: url('./images/orange_file.png') no-repeat;
        background-size: 100% 100%;
      }
      .coop_title_tag {
        width: 100%;
        height: 20px;
        margin-bottom: 15px;
        margin-top: 11px;
        .coop_title {
          width: 60px;
          height: 19px;
          font-size: 20px;
          font-weight: normal;
          color: #fdfeff;
          line-height: 20px;
          margin-right: 6px;
          float: left;
        }
        .coop_tag {
          margin-top: 5px;
          float: left;
          width: 75px;
          height: 14px;
          background: rgba(0, 0, 0, 0.2);
          border-radius: 7px;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 12px;
          transform: scale(0.92);
          font-weight: normal;
          color: #ffffff;
        }
      }
      .coop_desc {
        font-size: 12px;
        font-weight: normal;
        color: #333333;
        line-height: 18px;
        margin-bottom: 10px;
        display: block;
      }
    }
  }
}
</style>

```

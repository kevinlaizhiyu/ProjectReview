#

# 1. 实现原理

## 1.1 需求
订单状态分为 未付款、需补款、已付款、已退款、已取消五个状态，对应的编码是 1、2、3、4、5

未付款、需补款、已付款 对应流程如下
【提交订单】--->【支付订单】--->【交易完成】

取消订单 对应流程如下
【提交订单】--->【交易关闭】

已退款 对应流程如下
【提交订单】--->【支付订单】--->【退款成功】

## 1.2 思路

* 写一个 `initStep` 的方法，方法接收状态编码为入参，根据不同的状态编码进行匹配，赋值给一个变量 `steps`, `steps` 中写入许多具体的配置项。
  
* dom 读取 steps 中的配置项，并进行渲染

* steps 中包含两个对象 或者包含三个对象，这个根据需求视情况而定

* steps 中的每一项代表的是每一个节点的状态 
```js
{
    name:'退款成功', //节点名称
    status:1, // 节点展示状态 0执行  1正在执行  2已经执行
    desc_date: moment(this.data.refundTime).format('YYYY-MM-DD'), //节点对应执行日期
    desc_time: moment(this.data.refundTime).format('HH:mm:ss') //节点对应执行时间
}
```
* dom 中循环steps ,渲染出两个或者三个节点

* 节点中的图标设置为 一个方法，读取当前节点展示 状态，返回对应的 className, 实现状态机的样式变化
```js
dotStatus(item.status); //返回className --->  'notyet'  'doing'  'done'
iconStatus(index,item.status,steps.length)// 同上类似
connectStatus(item.status,steps.length)// 同上类似
dotStatus(item.status)// 同上类似
```

这样只需要写好对应的css 属性就行

# 2. html
```html
<div class="order-steps-wrap">
  <template v-if="steps.length>0">
    <div 
     class="order-step-item" 
     v-for="(item,index) in steps" 
     :key="index+'order-step-item'"
    >
      <div :class="['order-step-dot',dotStatus(item.status)]">
        <i :class="['order-step-icon',iconStatus(index,item.status,steps.length)]"></i>
      </div>
      <div :class="['order-step-connect', connectStatus(item.status,steps.length)]" v-if="index!==0"></div>
      <div :class="['step-name',dotStatus(item.status)]">{{item.name||''}}</div>
      <div class="step-date">{{item.desc_date||''}}</div>
      <div class="step-time">{{item.desc_time||''}}</div>
    </div>
  </template>
</div>
```
# 3. js

```js
export default {
    methods:{
        initSteps(){
            let orderStatus = this.data.status
            switch (orderStatus) {
                case 1: //等待付款状态
                    this.steps = [
                        {
                            name:'提交订单',
                            status:1, //0还未做 1正在做 2已完成
                            desc_date: moment(this.data.createTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.createTime).format('HH:mm:ss')
                        },
                        {
                            name:'支付订单',
                            status:0,
                            desc_date:'',
                            desc_time:''
                        },
                        {
                            name:'交易完成',
                            status:0,
                            desc_date:'',
                            desc_time:''
                        },
                    ]
                    break;
                case 2:
                    this.steps = [
                        {
                            name:'提交订单',
                            status:2, //0还未做 1正在做 2已完成
                            desc_date: moment(this.data.createTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.createTime).format('HH:mm:ss')
                        },
                        {
                            name:'支付订单',
                            status:1,
                            desc_date: moment(this.data.payTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.payTime).format('HH:mm:ss')
                        },
                        {
                            name:'交易完成',
                            status:0,
                            desc_date:'',
                            desc_time:''
                        },
                    ]
                    break;
                case 3:
                    this.steps = [
                        {
                            name:'提交订单',
                            status:2, //0还未做 1正在做 2已完成
                            desc_date: moment(this.data.createTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.createTime).format('HH:mm:ss')
                        },
                        {
                            name:'支付订单',
                            status:2,
                            desc_date: moment(this.data.payTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.payTime).format('HH:mm:ss')
                        },
                        {
                            name:'交易完成',
                            status:1,
                            desc_date: moment(this.data.payTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.payTime).format('HH:mm:ss')
                        },
                    ]
                    break;
                case 4:
                    this.steps = [
                        {
                            name:'提交订单',
                            status:2, //0还未做 1正在做 2已完成
                            desc_date: moment(this.data.createTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.createTime).format('HH:mm:ss')
                        },
                        {
                            name:'交易关闭',
                            status:1,
                            desc_date: moment(this.data.closeTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.closeTime).format('HH:mm:ss')
                        },
                    ]
                    break;
                case 5:
                    this.steps = [
                        {
                            name:'提交订单',
                            status:2, //0还未做 1正在做 2已完成
                            desc_date: moment(this.data.createTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.createTime).format('HH:mm:ss')
                        },
                        {
                            name:'支付订单',
                            status:2,
                            desc_date: moment(this.data.payTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.payTime).format('HH:mm:ss')
                        },
                        {
                            name:'退款成功',
                            status:1,
                            desc_date: moment(this.data.refundTime).format('YYYY-MM-DD'),
                            desc_time: moment(this.data.refundTime).format('HH:mm:ss')
                        },
                    ]
                    break;
                default:
                    break;
            }
        },
        dotStatus(status){
            switch(status){
                case 0:
                    return 'notyet';
                case 1:
                    return 'doing';
                case 2:
                    return 'hasdone';
                default:
                    break;
            }
        },
        connectStatus(status,type){
            let class_name = ''
            switch(type){
                case 2:
                    class_name='two-step'
                    break;
                case 3:
                    class_name='three-step'
                    break;
                default:
                    break;
            }
            switch(status){
                case 0:
                    return `${class_name} disconnect`;
                case 1:
                    return `${class_name} connect`;
                case 2:
                    return `${class_name} connect`;
                default:
                    break;
            }
        },
        iconStatus(index=0,status=0,type=3){
            if(status===1&&type===3){
                switch(index){
                    case 0:
                        return 'icon-Submit-orders-s348'; //提交订单
                    case 1:
                        return 'icon-payment-s248'; //支付订单
                    case 2:
                        return 'icon-Order-completed-s348'; //交易完成
                    default:
                        break;
                }
            }else if(status===1&&type===2){
                switch(index){
                    case 0:
                        return '';
                    case 1:
                        return 'icon-Order-closed-s348'; //交易关闭
                    default:
                        break;
                }
            }else{
                return 'hidden'
            }
        }
    }
}
```
# 4. css

```less
.order-step-item{
      width: 16px;
      height: 16px;
      background: #eeeeee;
      border-radius: 50%;
      position: relative;
      .order-step-dot{
        position: absolute;
        top: 50%;
        left: 50%;
        width: 16px;
        height: 16px;
        transform: translate(-50%,-50%);
        border-radius: 50%;
        z-index: 2;
        display: flex;
        justify-content: center;
        align-items: center;
        &.notyet{
          background: #eeeeee;
        }
        &.hasdone{
          background: #ff6900;
        }
        &.doing{
          background: #ff6900;
          width: 26px;
          height: 26px;
        }
        .order-step-icon,
        .icon-Submit-orders-s348,
        .icon-payment-s248,
        .icon-Order-completed-s348,
        .icon-Order-closed-s348{
          font-size: 18px;
          margin-left: 1px;
          color: #fff;
          &.icon-Submit-orders-s348{
             
          }
          &.icon-payment-s248{
            
          }
          &.icon-Order-completed-s348{
            
          }
          &.icon-Order-closed-s348{
            
          }
          &.hidden{
            display: none;
          }
        }
      }
      .order-step-connect{
        position: absolute;
        top: 6px;
        right: 0;
        height: 3px;
        opacity: 1;
        z-index: 1;
        &.three-step{
          width: @step_width/2;
        }
        &.two-step{
          width: @step_width;
        }
        &.connect{
          background: #ff6900;
        }
        &.disconnect{
          background: #eeeeee;
        }
      }
      .step-name{
        position: absolute;
        width: 56px;
        height: auto;
        left: 50%;
        top: 28px;
        transform: translateX(-50%);
        font-size: 14px;
        font-weight: 400;
        color: #333333;
        &.notyet{
          color: #999;
        }
        &.doing{
          color: #ff6800;
        }
        &.hasdone{
          color: #333;
        }
      }
      .step-date{
        position: absolute;
        width: 70px;
        height: auto;
        left: 50%;
        top: 47px;
        white-space: nowrap;
        transform: translateX(-50%);
        color: #999999;
        font-size: 12px;
      }
      .step-time{
        position: absolute;
        width: 50px;
        height: auto;
        left: 50%;
        top: 63px;
        white-space: nowrap;
        transform: translateX(-50%);
        color: #999999;
        font-size: 12px;
      }
    }
```

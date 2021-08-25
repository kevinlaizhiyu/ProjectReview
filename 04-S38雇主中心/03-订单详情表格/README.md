#

# 1. 表头样式设置
`:show-header="true"`
`:header-cell-class-name="setHeaderClassName"`

# 2. 表格中如何使用自定义模板

```html
<el-table-column
  prop="sbName"
  label="服务内容"
  min-width="288">
  <template slot-scope="scope">
    <div :class="['service-name',scope.row.hasTag && data.orderType===1?'has_tag':'no_tag']">{{ scope.row.sbName }}</div>
    <div class="addtion-tag" v-if="scope.row.hasTag">
      <span>附加</span>
    </div>
  </template>
</el-table-column>
```

# 3. 如何进行行合并列合并

* 仔细参考element 的官方文档，我们在设置函数合并 某一行或者某一列的时候，不要忘记条件所涉及的其他的行或者列也是需要设置的


* 例如：合并第一列第第一行第二行，那么第一行就要设置 {rowspan:2,colspan:1} ,第二行就要设置 {rowspan:0,colspan:0} ,此处表示不显示,除此外第一列其余位置都要设置 {rowspan:1,colspan:1}，此处表示作为你一个 正常单元显示。


* **必须要考虑很全，否则表格就会出现错误**

```js
export default {
    methods:{
        objectSpanMethod({ row, column, rowIndex, columnIndex }) {
            let main = this.mainServiceLength; //2
            let addtion = this.addtionalServiceLength; //3 
            let total = Number(main)+Number(addtion);//5

            if (columnIndex === 2) {
                if (rowIndex === 0) {
                    return {
                        rowspan: main||addtion,
                        colspan: 1
                    };
                } else if(rowIndex === main ){
                    return {
                        rowspan: addtion,
                        colspan: 1
                    };
                }else{
                    return {
                        rowspan: 0,
                        colspan: 0
                    }
                }
            }
            if (columnIndex === 3) {
                if (rowIndex === 0) {
                    return {
                        rowspan: total,
                        colspan: 1
                    };
                }else{
                    return {
                        rowspan: 0,
                        colspan: 0
                    }
                }
            }
        },
    }
}



```
# 4. 动态数据

使用 `elementUI` 的 `table` 组件，需要 `tableData` 的数据来进行 相应的渲染，我们在 `mounted` 中初始化这些数据即可。

```js
export default {
    methods:{
        initTable(){
            let {orderProductList=[], additionalServicePrice='', productServicePrice='', additionalOrderProductList=[],activity={},ticketActivity={} }= this.data;

            let mainService = [] ;
            let subService = [];
            if(orderProductList.length>0){
                this.mainServiceLength = orderProductList.length
                orderProductList.forEach( (ele,idx) => {
                    mainService.push({
                        sbName:`${ele.sbName}${ele.serviceExpandName?':'+ele.serviceExpandName : ''}`,
                        amount:this.showServiceUnit(ele),
                        rules:{},
                        hasTag:false,
                    });
                });
                mainService[0].payment = `产品总额：￥${productServicePrice}`;
            }

            if(additionalOrderProductList.length>0){
                this.addtionalServiceLength = additionalOrderProductList.length;
                additionalOrderProductList.forEach((ele,idx)=>{
                    subService.push({
                        sbName:this.isCourseOrder?`视频课程`:`${ele.sbName}${ele.serviceExpandName?':'+ele.serviceExpandName : ''}`,
                        amount:this.isCourseOrder?'1个':this.showServiceUnit(ele),
                        rules:{},
                        hasTag:this.shouldTagShow
                    })
                })
                subService[0].payment = `${ this.data.orderType===1?'附加总额：':'服务总额：'}￥${additionalServicePrice}`;
            }

            let tempContainer = [...mainService,...subService];

            if(activity){
                tempContainer[0].rules.activity = activity
            }

            if(ticketActivity){
                tempContainer[0].rules.ticketActivity = ticketActivity
            }
            this.tableData = tempContainer
        },
    }
}
```

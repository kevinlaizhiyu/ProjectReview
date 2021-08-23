# 

# 1. element 表头自定义

`:show-header="true"`
`:header-cell-class-name="setHeaderClassName"`


```html
<el-table
  class='wow fadeInUp'
  :data="tableData"
  :span-method="objectSpanMethod"
  :show-header="true"
  :header-cell-class-name="setHeaderClassName"
  :fit="true"
  border
  style="width: 100%"
></el-table>
```

```js
setHeaderClassName() {
    return 'head_style';
}
```

```css
/deep/.head_style {
      height: 50px;
      background: #d2d8e6;
      text-align: center;
      font-size: 16px;
      font-weight: bolder;
      color: #3a3c49;
    }
```
# 2. element 表格宽度自定义

`width="168"` 默认给的单位是 px 

```html
<el-table-column prop="edition" label="企业/个人版" width="168"></el-table-column>
```

# 3. element 表格合并操作注意事项

`:span-method="objectSpanMethod"`

```js
export default {
    methods:{
        objectSpanMethod({row, column, rowIndex, columnIndex}) {
            if (columnIndex === 0 || columnIndex === 4) {
                if (rowIndex === 0) {
                    return {
                        rowspan: 2,
                        colspan: 1,
                    };
                } else if (rowIndex === 1) {
                    return {
                        rowspan: 0,
                        colspan: 0,
                    };
                }
                return {
                    rowspan: 1,
                    colspan: 1,
                };
            }
        },
    }
}
```
>! 【注意】\
> 在这里进行合并的时候不仅仅要对合并的目标单元格进行配置，还需要对非目标单元格进行设置 \
> 
> 譬如：\
> 上边代码的意思是，当为第一列或者第五列的时候，如果是第一行，则合并两行为1行，列保持一列；当为第二行的时候，第二行第二列的单元格保持0个单元格，除此之外的所有第一列和第五列的单元格都是保持一个单元格的行和列

要将合并行（列）对应的行（列）合并的情况考虑完全，否则就会出现表格错位的bug

# 4. 表格内容、样式自定义实现 

自定义操作比较容易，渲染一个 `<template></template>` 即可

* `slot-scope="scope"` 必须要，表示的是插入数据；
* `maker_table_edition` 在 ` .el-table__body ` 中定义；
* `scope.row.edition.ename` 引用传入的表格数据

```html
<el-table-column prop="edition" label="企业/个人版" width="168">
  <template slot-scope="scope">
    <div :class="['maker_table_edition', scope.row.edition.eclass]">
      <div class="edition_name">{{ scope.row.edition.ename }}</div>
      <div class="edition_list">
        <span
          class="edition_item"
          v-for="(item, index) in scope.row.edition.elist"
          :key="index + 'edition_item'"
          >{{ item }}</span
        >
      </div>
    </div>
  </template>
</el-table-column>
```

# 5. 表格渲染的数据结构

```html
<el-table-column prop="edition" label="企业/个人版" width="168"></el-table-column>
```
`prop="edition"`  表示的是继承数据的名称；

`slot-scope="scope"` 实现数据插槽；

`scope.row.edition.ename` 实现数据引用；

具体数据根据自己需要来实现，大致都是

```js
[
    {
        property1:{},
        property2:[],
        ...
    },
    {
        property1:{},
        property2:[],
        ...
    },
    {
        property1:{},
        property2:[],
        ...
    },
    ...
]
```

```js
export default {
    data(){
        return {
            tableData: [
                {
                    edition: {
                        eclass: 'company_edition',
                        ename: '企业版',
                        elist: [
                            '工商类',
                            '代理记账类',
                            '律所类',
                            '会计师事务所',
                            '税务师事务所',
                            '创客空间',
                            '孵化器',
                            '众创空间',
                            '知识产权类',
                        ],
                    },
                    classify: {
                        cclass: '',
                        cname: '金牌合伙人',
                    },
                    coreRights: {
                        co_class: '',
                        co_list: [
                            '· 专业税筹知识线上赋能培训（价值1288元）',
                            '· 税筹业务拓展全流程步骤赋能工具包',
                            '· 优质税筹园区资源共享',
                            '· 热销产品专业赋能培训',
                            '· 全品类产品超低渠道价',
                        ],
                    },
                    valueAdded: {
                        va_class: '',
                        va_list: [
                            '· 会员身份牌匾',
                            '· 产品售卖物料支持',
                            '· 财商学院精品课程一期（价值888元）',
                            '· 税筹业务返税阶梯返佣 · 地方城市线下赋能',
                            '· 八戒招聘企业版免费体验',
                            ' · 免费400电话赠送（价值2400元）',
                            '· 免费赠送SaaS代账软件基础版一套（150账套）（价值2988元）',
                        ],
                    },
                    valueAttached: {
                        at_class: '',
                        at_list: [
                            '· 属地化税筹业务分润',
                            '· 工商代办及代账客户不限量推送',
                            '· 优先享受政府合作业务消化',
                            '· 线下“造神计划”训练营',
                        ],
                    },
                },
            ],
        }
    }
}
```

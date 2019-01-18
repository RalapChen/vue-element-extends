# vue-element-extends

 [查看博客](https://blog.csdn.net/xlz26296/article/details/80195408)

![editable](https://github.com/xuliangzhan/vue-element-extends/blob/master/src/assets/editable1.gif?raw=true)

## API

### Editable Attributes

```html
<el-editable ref="editable" edit-config="{trigger: 'click'}"></el-editable>
```

| 属性 | 类型 | 描述 | 默认值 |
|------|------|-----|-----|
| trigger | String | 触发方式（'manual'手动方式, 'click'点击触发编辑, 'dblclick'双击触发编辑） | 'click' |
| mode | String | 编辑方式（'cell'列编辑模式, 'row'行编辑模式） | 'cell' |
| icon | Boolean | 是否显示列头编辑图标 | true |
| status | Boolean | 是否显示列的编辑状态 | true |

### Editable Methods

| 属性 | 描述 | 参数 |
|------|------|-----|
| reload(datas) | 初始化加载数据 |  |
| revert() | String | 还原修改之前数据 |  |
| insert(record) | 插入一行 |  |
| insertAt(record, rowIndex) | 指定位置插入一行 |  |
| remove(record) | 删除行 |  |
| removes(records) | 删除多行 |  |
| removeRow(rowIndex) | 删除行 |  |
| removeRows(rowIndexs) | 删除多行 |  |
| removeSelecteds() | 删除选中行 |  |
| clear() | Boolean | 清空所有数据 |  |
| clearActive() | 清除所有活动行列为不可编辑状态 |  |
| setActiveRow(rowIndex) | 设置活动行为可编辑状态（只对mode='row'有效） |  |
| updateStatus(scope) | 更新列状态（当使用自定义组件时，值发生改变时需要调用来更新列状态） |  |
| getRecords() | 获取表格数据 |  |
| getAllRecords() | 获取表格所有数据 |  |
| getInsertRecords() | 获取插入数据 |  |
| getRemoveRecords() | 获取删除数据 |  |
| getUpdateRecords()| 获取修改数据 |  |

### Editable-Column Attributes

```html
<el-editable-column ref="editable" edit-render="{name: 'ElInput'}"></el-editable-column>
```

| 属性 | 类型 | 描述 | 默认值 |
|------|------|-----|-----|
| name | String | 渲染的组件名称（'ElInput', 'ElSelect', 'ElCascader', 'ElDatePicker', 'ElInputNumber', 'ElSwitch'） | 'ElInput' |
| type | String | 渲染类型（'default'组件激活后才可视,'visible'组件一直可视） | 'default' |
| attrs | Boolean | 渲染组件附加属性 | {} |
| optionAttrs | Boolean | 下拉组件选项附加属性（只对name='ElSelect'有效） | {} |
| options | Boolean | 下拉组件选项列表（只对name='ElSelect'有效） | [] |

### Editable-Column Scoped Slot

| 属性 | 描述 |
|------|------|
| --- | 自定义渲染显示内容，参数为 { row, column, $index } |
| type | 自定义渲染组件，参数为 { row, column, $index } |
| head | 自定义表头的内容，参数为 { column, $index } |

## Example

* 全局事件需要依赖 vuex 中的 click 事件 (参考store/modules/event.js)
* 将 Editable.vue 和 EditableColumn.vue 复制到自己项目的 components 目录下
* 然后在 main.js 引入组件即可

```JavaScript
import Editable from '@/components/Editable.vue'
import EditableColumn from '@/components/EditableColumn.vue'

Vue.component(Editable.name, Editable)
Vue.component(EditableColumn.name, EditableColumn)
```

```html
<template>
  <div>
    <h1>基于 ElementUI 可编辑表格</h1>
    <el-button type="primary" @click="$refs.editable.insert({name: 'new1'})">插入一行</el-button>
    <el-button type="primary" @click="$refs.editable.insertAt({name: 'new1'}, -1)">在最后插入一行</el-button>
    <el-button type="danger" @click="$refs.editable.removeRows([0, 2])">删除指定行[0, 2]</el-button>
    <el-button type="danger" @click="$refs.editable.removeSelecteds()">删除选中</el-button>
    <el-button type="danger" @click="$refs.editable.revert()">还原更改</el-button>
    <el-button type="danger" @click="$refs.editable.clear()">清空所有</el-button>
    <el-editable ref="editable" :data.sync="list">
      <el-editable-column type="selection" width="55"></el-editable-column>
      <el-editable-column type="index" width="55"></el-editable-column>
      <el-editable-column prop="name" label="名字"></el-editable-column>
      <el-editable-column prop="sex" label="性别" :editRender="{name: 'ElSelect', options: sexList}"></el-editable-column>
      <el-editable-column prop="age" label="年龄" :editRender="{name: 'ElInputNumber', attrs: {min: 1, max: 200}}"></el-editable-column>
      <el-editable-column prop="region" label="地区" :editRender="{name: 'ElCascader', attrs: {options: regionList}}"></el-editable-column>
      <el-editable-column prop="birthdate" label="出生日期" :editRender="{name: 'ElDatePicker', attrs: {type: 'date', format: 'yyyy-MM-dd'}}"></el-editable-column>
      <el-editable-column prop="date1" label="选择日期" :editRender="{name: 'ElDatePicker', attrs: {type: 'datetime', format: 'yyyy-MM-dd hh:mm:ss'}}"></el-editable-column>
      <el-editable-column prop="flag" label="是否启用" :editRender="{name: 'ElSwitch'}"></el-editable-column>
      <el-editable-column prop="remark" label="备注" :editRender="{name: 'ElInput'}"></el-editable-column>
      <el-editable-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" type="danger" @click="removeEvent(scope.row, scope.$index)">删除</el-button>
        </template>
      </el-editable-column>
    </el-editable>
  </div>
</template>

<script>
import { MessageBox } from 'element-ui'

export default {
  data () {
    return {
      sexList: [
        {
          label: '男',
          value: '1'
        },
        {
          label: '女',
          value: '0'
        }
      ],
      regionList: [
        {
          value: 'bj',
          label: '北京',
          children: [
            {
              value: 'bjs',
              label: '北京市',
              children: [
                {
                  value: 'dcq',
                  label: '东城区'
                }
              ]
            }
          ]
        },
        {
          value: 'gds',
          label: '广东省',
          children: [
            {
              value: 'szs',
              label: '深圳市',
              children: [
                {
                  value: 'lhq',
                  label: '罗湖区'
                }
              ]
            },
            {
              value: 'gzs',
              label: '广州市',
              children: [
                {
                  value: 'thq',
                  label: '天河区'
                }
              ]
            }
          ]
        }
      ],
      list: [
        {
          name: 'test11',
          height: 176,
          age: 26,
          sex: '1',
          region: null,
          birthdate: new Date(1994, 0, 1),
          date1: new Date(2019, 0, 1, 20, 0, 30),
          remark: '备注1',
          flag: false
        },
        {
          name: 'test22',
          height: 166,
          age: 24,
          sex: '0',
          region: ['gds', 'szs', 'lhq'],
          birthdate: new Date(1992, 0, 1),
          date1: new Date(2019, 0, 1, 12, 10, 30),
          remark: '备注2',
          flag: true
        },
        {
          name: 'test33',
          height: 172,
          age: 22,
          sex: '1',
          region: ['bj', 'bjs', 'dcq'],
          birthdate: new Date(1990, 0, 1),
          date1: new Date(2019, 0, 1, 0, 30, 50),
          remark: null,
          flag: false
        }
      ]
    }
  },
  methods: {
    removeEvent (row, index) {
      MessageBox.confirm('确定删除该数据?', '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$refs.editable.removeRow(index)
      }).catch(e => e)
    }
  }
}
</script>
```
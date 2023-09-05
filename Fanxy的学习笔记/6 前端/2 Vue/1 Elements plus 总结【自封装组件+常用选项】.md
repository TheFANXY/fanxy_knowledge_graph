# 1. 封装自用组件

## 1. 常用增删改查模板页

### 1.0. 查询封装 

**结合参数的复杂查询，如果是 `post` 直接封装参数到 `data` 中，而不是 `get` 的 `config` 中的 `params`**

```js
// 获取文章列表
export const artGetListService = (params) =>
  request.get('/my/article/list', {
    params
  })
```

```js
// 获取文章列表
const getArticleList = async () => {
  loading.value = true // 表格的加载动画
  const res = await artGetListService(params.value)
  if (res.data.code !== 0) { // 结合业务code
    ElMessage.error(`${res.data.message}`)
  } else {
    articleList.value = res.data.data
    total.value = res.data.total // 这个是绑定前台查出一共多少条数据 后台应该就是 selet count(*) from t_xxx where xxxx 分页展示的
    loading.value = false 
  }
}
// 开局挂载
onMounted(() => {
  getArticleList()
})
```



### 1.1. 表单表格

```js
    <!--表单区域和下拉框复杂搜索-->
    <el-form inline>
      <el-form-item label="文章分类:">
        <channel-select v-model="params.cate_id"></channel-select>
      </el-form-item>
      <el-form-item label="发布状态:">
        <el-select v-model="params.state">
          <el-option label="已发布" value="已发布"></el-option>
          <el-option label="草稿" value="草稿"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary">搜索</el-button>
        <el-button>重置</el-button>
      </el-form-item>
    </el-form>
    <!--表格区域-->
    <el-table :data="articleList" v-loading="loading">
      <el-table-column label="文章标题" prop="title">
        <template #default="{ row }">
          <el-link :underline="false" type="primary">{{ row.title }}</el-link>
        </template>
      </el-table-column>
      <el-table-column label="分类" prop="cate_name"></el-table-column>
      <el-table-column label="发表时间" prop="pub_date">
        <template #default="{ row }">
          {{ dateFormat(row.pub_date) }}
        </template>
      </el-table-column>
      <el-table-column label="状态" prop="state"></el-table-column>
	  <!-- 按钮区域 -->
      <el-table-column label="操作">
        <template #default="{ row }">
          <el-button
            :icon="Edit"
            circle
            plain
            type="primary"
            @click="onEditArticle(row)"
          ></el-button>
          <el-button
            :icon="Delete"
            circle
            plain
            type="danger"
            @click="onDeleteArticle(row)"
          ></el-button>
        </template>
      </el-table-column>
	 <!--数据为空的状态-->
      <template #empty>
        <el-empty description="当前数据为空"></el-empty>
      </template>
    </el-table>
```



### 1.2. 复杂查询子组件下拉框

**绑定父组件DTO查询参数**

父组件 使用 `v-model` 相当于给子组件绑定了 `:modelValue` 和 `:@update:modelValue` ，尽管可以自定义，即如果 `v-model:xxx="mydefine"`，但是子组件的 `attribute` 不变，只是传下来的接受的 `props` 变了，需要把对应的 `props` 和 子传父的 `emit` 方法中的参数变了。

**如下。**

```js 
// 父传子的配置
defineProps({
  mydefine: {
    type: [String, Number]
  }
})

// 子传父的数据
const emit = defineEmits(['update:mydefine'])

  <el-select
    :modelValue="mydefine"
    @update:modelValue="emit('update:mydefine', $event)"
  >
```

> 为了防止搞乱了，建议父组件直接写 `v-model=""`，不要自定义，直接父子都默认使用 `modelValue`。

**子组件 下拉框**

```js
<script setup>
import { artGetChannelsService } from '@/api/article'
import { ref, onMounted } from 'vue'

// 父传子的配置
defineProps({
  modelValue: {
    type: [String, Number]
  }
})

// 子传父的数据
const emit = defineEmits(['update:modelValue'])

// 获取分类列表
const channelList = ref([])

const getChannelList = async () => {
  const res = await artGetChannelsService()
  channelList.value = res.data.data
}

// 挂载钩子
onMounted(() => {
  getChannelList()
})
</script>

<template>
  <el-select
    :modelValue="modelValue"
    @update:modelValue="emit('update:modelValue', $event)"
  >
    <el-option
      v-for="channel of channelList"
      :key="channel.id"
      :label="channel.cate_name"
      :value="channel.id"
    ></el-option>
  </el-select>
</template>

<style scoped></style>
```



**父组件，一个页面，需要绑定一个和分页，条件查询等信息的参数，用来子组件如果更新，给父组件的参数对象进行更新值。**

```js
// 一般放在对应页面 这是封装 dto 查询参数的 很重要 可以设置默认情况 然后和上面选项框进行父子通信
const params = ref({
  pagenum: 1,
  pagesize: 4,
  cate_id: '',  // 可【替换或者增加】实际的数据 这里是 dto 的参数
  state: ''     // 这个是关于一个枚举状态的查询 
})

const total = ref(0) // 这个是绑定前台查出一共多少条数据 后台应该就是 selet count(*) from t_xxx where xxxx

<template>
<!--表单区域-->
<el-form inline>
  <el-form-item label="文章分类:">
    <channel-select v-model="params.cate_id"></channel-select>
  </el-form-item>
.........................................................................【省略】
```



**下拉框的主动查询和重置**

```js
// 下拉框查询操作
const onSearch = () => {
  params.value.pagenum = 1
  getArticleList()
}
// 下拉框重置操作
const onReset = () => {
  params.value.pagenum = 1
  params.value.cate_id = ''
  params.value.state = ''
  getArticleList()
}
```



### 1.3. 复杂分页表格

一般放在表格下面，严格来说应该是 `footer` 之上【`footer` 一般都已经被封装在 `layout` 里面了】，其他组件之下，但是可以自己调整。

```js
    <!--分页区域-->
    <el-pagination
      v-model:current-page="params.pagenum"
      v-model:page-size="params.pagesize"
      style="margin-top: 20px; justify-content: flex-end"
      :page-sizes="[4, 8, 10, 12]"
      :background="true"
      layout="jumper, total, sizes, prev, pager, next"
      :total="total"
      //:small="small"    这俩是关于尺寸和失效的控制 可以结合变量或者干脆不设置
      //:disabled="disabled"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    />
```

这是和上面的页面尺寸和页面跳转绑定的参数

```js
// 单页尺寸变更
const handleSizeChange = (size) => {
  params.value.pagenum = 1
  params.value.pagesize = size
  getArticleList()
}

// 页面跳转变更
const handleCurrentChange = (page) => {
  params.value.pagenum = page
  getArticleList()
}
```



### 1.4. 增加数据的抽屉组件





# 2. 一些快速添加的组件和`api`

## 1. 给表格组件或者是其他组件绑定为空的虚拟组件

```js
    <el-table>
       .....................省略
	  <template #empty>
        <el-empty description="当前数据为空"></el-empty>
      </template>
    </el-table>
```



## 2. 日期函数的封装

1. 新建 `utils/format.js` 封装格式化日期函数

```jsx
import { dayjs } from 'element-plus'

export const dateFormat = (time) =>
  dayjs(time).format('YYYY年-MM月-DD日 HH:mm:ss')
```

2. 导入使用

```vue
import { formatTime } from '@/utils/format'

<el-table-column label="发表时间">
  <template #default="{ row }">
    {{ formatTime(row.pub_date) }}
  </template>
</el-table-column>
```

## 3. 

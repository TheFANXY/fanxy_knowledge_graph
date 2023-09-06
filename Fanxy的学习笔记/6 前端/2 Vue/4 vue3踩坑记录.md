# 1. vue相关

## 1.1. `pinia`相关

### 1. `pinia` 是基于内存的

基于用户的信息，在所有页面的模板，设置了进入页面就会通过方法获取用户的信息，而表单，想要绑定数据，本来靠获取 `pinia` 的数据，但是大问题是，如果刷新页面，`pinia` 的数据会丢失，而渲染页面的时候，未必会先渲染父组件，所以为了能实时获取数据，可以使用 `watchEffect` 实时获取数据给表单的数据赋值，即便是父组件后渲染，也能获取到值给子组件的元素。

```js
// pinia 仓库
const userStore = useUserStore()

// 加载 loading
const loading = ref(false)

// 仓库头像的地址 base64 img 文件
const imgUrl = ref()
watchEffect(() => {
  if (!userStore.user.user_pic) {
    loading.value = true
  } else {
    loading.value = false
  }
  imgUrl.value = userStore.user.user_pic
})
// 上传表单的 ref
const uploadRef = ref()
```





# 2. 组件库相关









# 3. 基本语法相关
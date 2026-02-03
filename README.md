# 简介

> - 支持多种文件: `png,apng,bmp,jpg,jpe,jpeg,gif,svg,webp,heif,mp4,webm,mov,docx,xlsx,pdf,pptx`预览的vue3组件
> - 其中`docx,xlsx,pdf,ppt`来自大佬[@vue-office](https://github.com/501351981/vue-office)
> - 对于`@vue-office中pdf`不兼容的浏览器使用`embed`进行兜底

```text
png,apng,bmp,jpg,jpe,jpeg,gif,svg,webp,heif,mp4,webm,mov,docx,xlsx,pdf,pptx
```

## 安装

```sh
npm install @ansstory/file-viewer
```

## 使用示例

```js
// 全局引入自动注册
import FileViewer from '@ansstory/file-viewer'
import '@ansstory/file-viewer/dist/file-viewer.css'
app.use(FileViewer, {
  // 全局配置
  name: 'FileViewer', // 组件名
  hiddenArrow: false, // 是否隐藏箭头
  hiddenFileName: false, // 是否隐藏文件名
  zIndex: 9999, // 弹窗的 z-index
  fileApi: '', // 内部已做兼容处理：非全地址则拼接代理
  infinite: false, // 是否可以无限循环预览
  zoomRate: 0.1, // 缩放比例
  maxScale: 6, // 最大缩放比例
  minScale: 0.2, // 最小缩放比例
  teleported: false, // 是否插入至 body 元素上
  // toolbar: true, // 是否显示工具栏
  toolbar: ['dArrowLeft', 'zoomOut', 'zoomIn', 'reset', 'rotateLeft', 'rotateRight', 'mirror', 'download', 'dArrowRight'], // 或者自定义工具栏
  displayFileName(url) {
    // 你可以在此重写预览的文件名
    // url = '/static/测试文件_4342343.pdf' // 默认展示：测试文件.pdf
    const lastSlashIndex = url.lastIndexOf('/')
    if (lastSlashIndex === -1) {
      return url
    }
    return url.substring(lastSlashIndex + 1)
  },
})
```

```html
<script setup lang="ts">
  // 或者 单文件引入
  import { FileViewer } from '@ansstory/file-viewer'
  import '@ansstory/file-viewer/dist/file-viewer.css'
  import { ref, useTemplateRef } from 'vue'
  // 逗号分割的字符串
  // const fileList = ref('https://test-jpfile1.oss-cn-shenzhen.aliyuncs.com/Bom/bom/2022/1/19/2022011911370824626513.pdf')
  // 或者
  const fileList = ref(['https://test-jpfile1.oss-cn-shenzhen.aliyuncs.com/Bom/bom/2022/1/19/2022011911370824626513.pdf'])
  const FileViewerRef = useTemplateRef('FileViewerRef')
  const openFileViewer = () => {
    FileViewerRef.value?.open()
  }
  const displayFileName = (url) => {
    const fileName = url?.split('/')?.pop()
    return fileName?.replace(/_.*?\./, '.') || url
  }
</script>

<template>
  <button type="primary" @click="openFileViewer">open</button>
  <!-- ! props将覆盖全局配置 -->
  <FileViewer ref="FileViewerRef" :urls="fileList" fileApi="/zzz" :maxScale="8" :displayFileName="displayFileName" />
</template>
```

## Attributes

| 参数            | 说明                        | 类型                          | 可选值                                                                                                                            | 默认值                                                                                                           |
| --------------- | --------------------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| urls            | 文件地址                    | `string/array`                | -                                                                                                                                 | `''`                                                                                                             |
| urlIndex        | 预览第几个文件(索引)        | `number`                      | -                                                                                                                                 | `0`                                                                                                              |
| minScale        | 预览文件的最小缩放比例      | `number`                      | -                                                                                                                                 | `0.2`                                                                                                            |
| zoomRate        | 缩放倍数                    | `number`                      | -                                                                                                                                 | `0.1`                                                                                                            |
| maxScale        | 预览文件的最大缩放比例      | `number`                      | -                                                                                                                                 | `4`                                                                                                              |
| hiddenFileName  | 隐藏文件名称                | `boolean`                     | -                                                                                                                                 | `false`                                                                                                          |
| hiddenArrow     | 隐藏左右箭头                | `boolean`                     | -                                                                                                                                 | `false`                                                                                                          |
| infinite        | 是否可以无限循环预览        | `boolean`                     | -                                                                                                                                 | `false`                                                                                                          |
| teleported      | 是否插入至 `body` 元素上    | `boolean`                     | -                                                                                                                                 | `false`                                                                                                          |
| zIndex          | 设置图片预览的 `z-index`    | `number`                      | -                                                                                                                                 | `9999`                                                                                                           |
| fileApi         | 代理Api：非全地址则拼接代理 | `string`                      | -                                                                                                                                 | `''`                                                                                                             |
| isWebDownload   | 是否前端下载                | `boolean`                     | -                                                                                                                                 | 当为`false`时可通过`download`自定义下载                                                                          |
| toolbar         | 工具栏                      | `array/boolean`               | `['dArrowLeft', 'zoomOut', 'zoomIn', 'reset', 'rotateLeft', 'rotateRight', 'mirror', 'download', 'dArrowRight'] or true or false` | `['dArrowLeft', 'zoomOut', 'zoomIn', 'reset', 'rotateLeft', 'rotateRight', 'mirror', 'download', 'dArrowRight']` |
| displayFileName | 重写预览的文件名            | `Function(url:string):string` | -                                                                                                                                 | `url?.split('/')?.pop()?.replace(/_.*?\./, '.')`                                                                 |

## Events

| 事件名   | 说明         | 参数                                                                               |
| -------- | ------------ | ---------------------------------------------------------------------------------- |
| close    | 关闭回调     | -                                                                                  |
| switch   | 切换文件回调 | `{ index: number, url: string, fileName: string, fileType: string }`               |
| download | 下载回调     | `{ index: number, url: string, fileName: string, fileType: string }`               |
| load     | 加载完成回调 | `Event/undefined`                                                                  |
| error    | 加载失败回调 | `{ index: number, url: string, fileName: string, fileType: string, error: Error }` |

## Exposes

| 事件名 | 说明 | 参数 |
| ------ | ---- | ---- |
| open   | 打开 | -    |
| close  | 关闭 | -    |

## Slots

| 名称    | 说明           | 参数                                                                                                                                                                                               |
| ------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| total   | 自定义操作区域 | `{ index: number; total: number; fileName: string; fileType: string }`                                                                                                                             |
| toolbar | 自定义操作区域 | `{ scale: (num: number) => void reset: () => void prev: () => void next: () => void rotate: (num: number) => void mirror: () => void download: () => void setActiveIndex: (num: number) => void }` |

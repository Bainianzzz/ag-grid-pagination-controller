# ag-grid翻页器demo

[翻页器组件代码](src/components/PagingTab.vue)

翻页器接收2个参数，绑定1个数组：

- allData：后端返回的所有数据
- pageSize：页面大小
- v-model绑定表格中所展示的数据

效果展示：
点击下箭头或者其左侧文本都可以切换pageSize

![翻页器效果](images/demonstration.png)
![翻页器效果_2](images/demonstration_2.png)

> ps:目前使用v-model绑定表格中所展示的数组，通过修改该数组实现翻页逻辑。如果后端不是一次性返回所有数据，翻页时需要重新进行http请求或者其他操作，可以监听自定义事件prevPage和nextPage，处理翻页逻辑。
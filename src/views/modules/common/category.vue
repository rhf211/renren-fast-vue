<template>

  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      node-key="catId"
      ref="menuTree"
      @node-click="nodeclick"
      :filter-node-method="filterNode"
      :highlight-current="true"
    ></el-tree>
  </div>
</template>

<script>
  export default {
    created() {
      this.getMenus();
    },
    data() {
      return {
        menus: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        },
        title: "",

      }
    },
    methods: {
      filterNode() {

      },
      //菜单列表
      getMenus() {
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'),
          method: 'get'
        }).then(({data}) => {
          this.menus = data.list
        })
      },
      //父子事件绑定
      nodeclick(data, node, component) {
        console.log("子组件category的节点被点击", data, node, component);
        //向父组件发送事件；
        this.$emit("tree-node-click", data, node, component);
      }
    }
  }
</script>

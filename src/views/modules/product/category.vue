<template>
  <div>
    <el-tree :data="menus" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        menus: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        }
      }
    },
    created () {
      this.handleNodeClick()
    },
    methods: {
      handleNodeClick (data) {
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'),
          method: 'get'
        }).then(({ data }) => {
          console.log('成功获取到菜单数据...', data.list)
          this.menus = data.list
        })

      }
    }
  }
</script>


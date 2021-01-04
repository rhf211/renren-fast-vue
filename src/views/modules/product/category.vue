<template>
  <div>
    <el-tree :data="menus" :props="defaultProps" @node-click="handleNodeClick"
             show-checkbox
             node-key="catId"
             draggable
             :expand-on-click-node="false"
             :default-expanded-keys="expandkey">
       <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level<=2"
            type="text"
            size="mini"
            @click="() => append(data)">
            添加
          </el-button>

          <el-button type="text" size="mini" @click="edit(data)">修改</el-button>
          <el-button
            v-if="node.childNodes.length==0"
            type="text"
            size="mini"
            @click="() => remove(node, data)">
            删除
          </el-button>
        </span>
      </span>
    </el-tree>

    <el-dialog
      :title="title"
      :visible.sync="dialogVisible"
      width="30%"
      :close-on-click-modal="false"
    >
      <el-form :model="category">
        <el-form-item label="分类名称">
          <el-input v-model="category.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="图标">
          <el-input v-model="category.icon" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="计量单位">
          <el-input v-model="category.productUnit" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitData">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        menus: [],
        expandkey: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        },
        title: "",
        dialogType: "", //edit,add
        dialogVisible: false,
        category: {
          name: "",
          parentCid: 0,
          catLevel: 0,
          showStatus: 1,
          sort: 0,
          productUnit: "",
          icon: "",
          catId: null
        },
      }
    },
    created() {
      this.handleNodeClick()
    },
    methods: {
      handleNodeClick(data) {
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'),
          method: 'get'
        }).then(({data}) => {
          this.menus = data.list
        })

      },
      append(data) {
        console.log('添加菜单', data)

        this.dialogType = "add";
        this.title = "添加分类";
        this.dialogVisible = true;
        this.category.parentCid = data.catId;
        this.category.catLevel = data.catLevel * 1 + 1;
      },

      edit(data) {
        console.log('修改菜单', data)
        this.dialogType = 'edit';
        this.title = '修改分类';
        this.dialogVisible = true;
        this.category.catId=data.catId;
        this.category.parentCid=data.parentCid;
        this.category.catLevel=data.catLevel;
      },

      submitData() {
        if (this.dialogType == "add") {
          this.addCategory();
        }
        if (this.dialogType == "edit") {
          this.editCategory();
        }
      },

      addCategory() {
        console.log('添加菜单')
        this.$http({
          url: this.$http.adornUrl('/product/category/save'),
          method: 'post',
          data: this.$http.adornData(this.category, false)
        }).then(({data}) => {
          this.$message({
            type: 'success',
            message: '保存成功' + data
          })
          //关闭对话框
          this.dialogVisible = false;
          this.handleNodeClick();
          this.expandkey = [this.category.parentCid]
        }).catch(() => {
          this.$message({
            type: 'error',
            message: '保存失败'
          })
          this.dialogVisible = false;
          this.handleNodeClick();
          this.expandkey = [this.category.parentCid]
        })
      },

      editCategory() {
        console.log('修改菜单edit',this.category)
        this.$http({
          url: this.$http.adornUrl('/product/category/update'),
          method: 'post',
          data: this.$http.adornData(this.category, false)
        }).then(({data}) => {
          this.$message({
            type: 'success',
            message: '修改成功'
          })
          this.dialogVisible=false;
          this.handleNodeClick();
          this.expandkey = [this.category.parentCid]
        }).catch(() => {
          this.$message({
            type: 'error',
            message: '修改失败'
          })
          this.dialogVisible=false;
          this.handleNodeClick();
          this.expandkey = [this.category.parentCid]
        })
      },

      remove(node, data) {
        this.$confirm('确定删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.$http({
            url: this.$http.adornUrl('/product/category/delete'),
            method: 'post',
            data: this.$http.adornData([data.catId], false)
          }).then(({data}) => {
            this.handleNodeClick();
            this.expandkey = [node.data.parentCid]
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消'
          })
        })
      },

    }
  }
</script>


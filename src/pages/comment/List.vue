<template>
  <div>
    <el-table :data="comments">
      <!--表格内容显示-->
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="userId" label="用户"></el-table-column>
      <el-table-column prop="articleId" label="文章"></el-table-column>
      <el-table-column prop="status" label="审核状态"></el-table-column>
      <el-table-column label="操作" fixed="right" align="center" width="150">
        <template slot-scope="scope">
          <el-button
            v-if="scope.row.status=='未审核'"
            type="info"
            icon="el-icon-bell"
            size="mini"
            @click="handleOpen(scope.row)"
          ></el-button>
        </template>
      </el-table-column>
      <!--展开显示详细-->
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="评论内容">
              <span>{{props.row.content}}</span>
            </el-form-item>
            <br />
            <el-form-item label="评论回复">
              <span>
                <el-tree :data="props.row.replyComments" :props="defaultProps" empty-text="无"></el-tree>
              </span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
    </el-table>
    <!-- 审核 -->
    <el-dialog title="审核内容" :visible.sync="dialogVisible" width="40%" :before-close="handleClose">
      <el-form :model="form">
        <el-form-item label="审核项" label-width="80px">
          <el-input v-model="form.id" readonly autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="审核">
          <el-radio-group v-model="form.status">
            <el-radio :label="true">审核通过</el-radio>
            <el-radio :label="false">审核未通过</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="内容" label-width="80px">
          <el-input type="textarea" v-model="form.content" readonly autocomplete="off"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="toExamin">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import request from "@/utils/request";
import qs from "querystring";
export default {
  // 为模板提供数据
  data() {
    return {
      dialogVisible: false,
      form: {},
      comments: [],
      defaultProps: {
        children: "replyComments",
        label: "content"
      }
    };
  },
  // 生命周期
  created() {
    this.reloadData();
  },

  // 方法，事件处理函数
  methods: {
    reloadData() {
      // 调用ajax查询所有文章数据
      let url = "http://localhost:3307/comment/findAllByCascade";
      request.get(url).then(result => {
        this.comments = result.data;
      });
    },
    toExamin() {
      request
        .request({
          url: "/comment/examine",
          method: "post",
          headers: {
            "Content-Type": "application/x-www-form-urlencoded"
          },
          data: qs.stringify(this.form)
        })
        .then(response => {
          // 提示信息
          this.$message({
            message: response.message,
            type: "success"
          });
          // 关闭模态框
          this.dialogVisible = false;
          // 重载数据
          this.reloadData();
      });
    },
    handleOpen(row) {
      this.dialogVisible = true;
      this.form = row;
    },
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then(_ => {
          done();
        })
        .catch(_ => {});
    }
  }
};
</script>
<style scoped>
</style>
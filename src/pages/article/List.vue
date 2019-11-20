<template>
  <div>
    <div class="btns">
      <el-button type="primary" size="small" @click="toPublishArticle">发布资讯</el-button>
    </div>
    <el-table :data="articles" @row-click="toReview">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="authorId" label="作者"></el-table-column>
      <el-table-column prop="category.name" label="栏目"></el-table-column>
      <el-table-column fixed="right" label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button
            @click="toDelete(scope.row.id)"
            type="danger"
            icon="el-icon-delete"
            size="mini"
          ></el-button>
          <el-button @click="toEdit(scope.row)" type="info" icon="el-icon-edit" size="mini"></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 文章内容 -->
    <el-dialog
      title="文章内容"
      lock-scroll
      center
      :visible.sync="dialogFormVisible"
      :show-close="false"
      fullscreen
    >
      <!-- 测试代码 -->
      <el-form :model="form">
        <el-form-item label="标题" label-width="60px">
          <div>{{form.title}}</div>
        </el-form-item>
        <el-form-item label="作者" label-width="60px">
           <div>{{form.authorId}}</div>
        </el-form-item>
        <el-form-item label="栏目" label-width="60px">
         <div> {{articles.category}} </div>
        </el-form-item>
        <el-form-item label="正文" label-width="60px" >
          <div>{{form.content}}}</div>
        </el-form-item>
        <el-form-item label="评论" label-width="60px">
          <el-button  type="info" icon="el-icon-edit" size="mini"></el-button>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button
          type="warning"
          icon="el-icon-close"
          size="small"
          @click="dialogFormVisible = false"
        ></el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import request from "@/utils/request";
export default {
  // 为模板提供数据
  data() {
    return {
      articles: [],
      form: {},
      dialogFormVisible: false
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
      let url = "http://127.0.0.1:3307/article/findAll";
      request.get(url).then(result => {
        this.articles = result.data;
      });
    },
    toEdit(row, column, event) {
      this.$router.push({ path: "/article/editor", query: row });
    },
    toDelete(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/article/remove";
        request.get(url, { params: { id } }).then(response => {
          // 通知
          this.$message({
            message: response.message,
            type: "success"
          });
          // 重载数据
          this.reloadData();
        });
      });
    },
    toReview(record) {
      this.dialogFormVisible = true;
      this.form = record;
      console.log(this.form);
    },
    toPublishArticle() {
      // 跳转
      this.$router.push({ path: "/article/editor" });
    },
    toEditComments(){

    }
  }
};
</script>
<style scoped>

</style>
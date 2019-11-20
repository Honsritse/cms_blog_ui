<template>
  <div>
    <el-button type="text" @click="back">返回</el-button>
    <!-- {{form}} -->
    <el-form ref="form" :model="form" label-width="80px">
       <el-form-item label="所属栏目">
          <el-select v-model="form.categoryId" placeholder="请选择所属栏目" clearable>
            <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>
      </el-form-item>
      <el-form-item label="文章标题">
        <el-input v-model="form.title"></el-input>
      </el-form-item>
      <el-form-item label="正文">
        <el-input type="textarea" v-model="form.content"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即创建</el-button>
        <el-button type="prmary" @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
  data(){
    return {
      form:{},
      categories:[]
    }
  },
  created(){
    this.form = this.$route.query;
    this.loadCategories();
  },
  methods:{
    loadCategories(){
      // 调用ajax查询所有文章数据
      let url = "category/findAll"
      request
      .get(url)
      .then(result => {
        this.categories = result.data;
      })
    },
    back(){
      this.$router.go(-1);
    },
    onSubmit(){
      // alert(JSON.stringify(this.form))
      // alert(qs.stringify(this.form));
      // 通过ajax将前端收集的数据this.form发送给服务接口
      let url = "/article/saveOrUpdate";
      request.request({
        url,
        method:"post",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:qs.stringify(this.form)
      })
      .then(result=>{
        this.$message({
          message:result.message,
          type:"success"
        });
        this.back();
      })
    },
    cancel(){
      this.$router.push({ path: "/article/list"});
    }
  }

}
</script>
<style scoped>

</style>
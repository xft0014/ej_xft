<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <el-table :data="customers">
            <el-table-column type="selection" width="55">
        </el-table-column>
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="categoryId" label="所属分类"></el-table-column>
            <el-table-column  label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination layout="prev, pager, next"  :total="50">
  </el-pagination>
  <el-dialog  title="录入产品信息" :visible.sync="visible" width="60%">

  <el-form :model="form" label-width="80px">
      <el-form-item label="产品名称">
         <el-input type="" v-model    ="form.name"></el-input>
      </el-form-item>
            <el-form-item label="产品价格">
         <el-input v-model="form.price"></el-input>
      </el-form-item>
      <el-form-item label="所属栏目">
         <el-select v-model="form.categoryId" placeholder="请选择">
      <el-option  v-for="item in options" :key="item.id"
             :label="item.name"
             :value="item.id"></el-option>
  </el-select>
      </el-form-item> 
          <el-form-item label="介绍">
         <el-input type="textarea" v-model="form.description"></el-input>
      </el-form-item>
          <el-form-item label="产品主图">
              只能上传jpg/png文件，且不超过500kb
         <el-input v-model="form.photo"></el-input>
      </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="visible = false">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>
<script>
import  request  from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        
        loadData(){
            let url = "http://localhost:6677/product/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.customers = response.data;
        })
        },
         submitHandler(){
      let url = "http://localhost:6677/product/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
           toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/product/deleteById?id="+id;
        // let that=this;
        request.get(url).then((response)=>{
            this.loadData();
        })
        this.$message({
        type: 'success',
        message: response.message
        });
      })
      
    },
        toUpdateHandler(row){
            this.visible = true;
            this.form=row;
        },
        closeModelHandler(){
            this.visible = false;
        },
        toAddHandler(){
            this.visible = true;
              let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
        }
        }, 
    data(){
        return{
            water: {
                    siteName: '',
                },
                dataOptions: [],//下拉菜单
            visible:false,
            options:[],
            customers:[{id:1,name:'洗护服务'},{id:2,name:'洗鞋'}],
            form:{
                type:"产品信息"
            }
        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕之后要执行的操作
        //let url = "http://134.175.154.93:6677/customer/findAll"
         let url="http://localhost:6677/product/findAll"
        // let that = this
        request.get(url).then((response)=>{
            //将查询结果设置到customers中
            this.products = response.data;
        })
        this.loadData()
    }
}
</script>

<style scoped>

</style>
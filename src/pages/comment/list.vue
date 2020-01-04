<template>
    <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加评论</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        
        <el-table :data="comments">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="content" label="评论内容"></el-table-column>
            <el-table-column prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column prop="orderId" label="订单号"></el-table-column>
            <el-table-column fixed="right" label="操作">               
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        
         <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
         <el-dialog :title="title" :visible.sync="visible" width="60%" >
        <el-form :model="form" label-width="80px">
           
            <el-form-item label="评论内容">
                <el-input text="textarea" v-model="form.content"></el-input>
            </el-form-item>
            <el-form-item label="评论时间">
                <el-input v-model="form.commentTime"></el-input>
            </el-form-item>
            <el-form-item label="订单号">
                <el-input v-model="form.orderId"></el-input>
            </el-form-item>
            
        </el-form>
         <span slot="footer" class="dialog-footer">
         <el-button size="small" @click="closeModalHandler">取 消</el-button>
         <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
         </span>
        </el-dialog>      
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
     methods:{
        submitHandler(){
          let url="http://localhost:6677/comment/saveOrUpdate";
          request({
          url,
          method:"POST",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data: querystring.stringify(this.form)
          }).then((response)=>{
              this.closeModalHandler();
              this.loadData();
              this.$message({
                  type:"success",
                  message:response.message
              })
              
          })
             this.visible=false      
        },
        loadData(){
          let url = "http://localhost:6677/comment/findAll"
          request.get(url).then((response)=>{
          this.comments=response.data;
      })
        },
        toDeleteHandler(id){
        this.$confirm('此操作将永久删除该评论, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'  
        }).then(() => {
            let url="http://localhost:6677/comment/deleteById?id="+id;
            request.get(url).then((response)=>{
                this.loadData();
                this.$message({
            type: 'success',
            message: response.message
          });
        })
        
        })
        
        },
        toUpdateHandler(row){
            this.title="修改评论";
            this.form=row;
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
       toAddHandler(){
            this.title="添加评论";
            this.form={
                type:"comment"
            }
           this.visible=true;
       }
    },
    data(){
        return {
            visible:false,
            comments:[],
            form:{
                type:"comment"
            }
        }
    },
    created(){
        this.loadData();
    }

}
</script>

<style scoped>

</style>
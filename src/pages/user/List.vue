<template>
    <div class="user_list">
        <!--按钮 -->
        <div class="btns">
            <el-button type="primary" size="small"
            @click="toAdd">添加用户</el-button>
        </div> 
        <!--表格 -->
        <el-table :data="user" style="width: 100%">
            <el-table-column prop="username" label="用户名" width="180"></el-table-column>
            <el-table-column prop="realname" label="姓名" width="180"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="手机号码"></el-table-column>
            <el-table-column prop="status" label="状态"></el-table-column>
        <el-table-column fixed="right" label="操作" align="center" width="180"> 
            <template slot-scope="scope">
                <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
                <el-button @click="toDetail(scope.row.id)" type="text" size="small">详情</el-button>
                <el-button @click="toEdit(scope.row)" type="text" size="small">修改</el-button>
                <el-button @click="toUpdate(scope.row.id)" type="text" size="small">设置</el-button>
            </template>
        </el-table-column>
        </el-table>
        <!--模态框 -->
         <el-dialog title="新增用户信息" :visible.sync="dialogFormVisible">
            {{form}}
            <el-form :model="form">
                <el-form-item label="用户名" label-width="80px">
                <el-input v-model="form.username" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="姓名" label-width="80px">
                <el-input v-model="form.realname" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="性别" label-width="80px">
                <el-input v-model="form.gender" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="手机号码" label-width="80px">
                <el-input v-model="form.telephone" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="状态" label-width="80px">
                <el-input v-model="form.status" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogFormVisible = false">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandle">确 定</el-button>
            </div>
        </el-dialog>  
        <!--/模态框 -->
        <!--分页 -->
     
    </div>
</template>

<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
    data(){
        return{
            dialogFormVisible:false,
            form:{},
            user:[]
            
        }   
        
    },
    // 生命周期
    created(){
        this.relodeData();
    },
    //事件处理函数
    methods:{
        toEdit(record){
            //console.log(record)
            //拿到数据弹出模态框
            this.dialogFormVisible=true;
            this.form=record;
        },
        toDelete(id){
            //将id传到后端删除
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //交互
                let url ="/baseUser/deleteById"
                request.get(url,{params:{id}})
                .then(response=>{
                    //通知
                    this.$message({
                        message:response.message,
                        type:"success"
                    })
                    //重载数据
                    this.relodeData();
                })
                
            })
        },
        submitHandle(){
            request.request({
                url:'/baseUser/saveOrUpdate',
                method:'post',
                headers:{
                    'Content-Type':'application/x-www-form-urlencoded'
                },
                data:qs.stringify(this.form)
            }).then(response=>{
                this.$message({
                    //提示信息
                    message:response.message,
                    type:"success"
                })
                //关闭模态框
                this.dialogFormVisible=false;
                //重载数据
                this.relodeData();
            })
        },
         toAdd(){
             //先把form中的值置空
            this.form={};
             //点击按钮弹出模态框
             this.dialogFormVisible=true
         },
        
        relodeData(){
            //调用ajax查询所有用户数据
            let url ="/baseUser/cascadeRoleFindAll"
            request.get(url)
            .then(result=>{
            this.user=result.data;
        })
        },
        
    }
    
}

</script>

<style scoped>
    

</style>
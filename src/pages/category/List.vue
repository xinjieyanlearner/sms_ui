<template>
    <div class="articel_list">
        <!--按钮区域 -->
        <div class="btns">
            <el-button type="primary" size="small" @click="toAdd">新增栏目</el-button>
            <el-button type="danger" size="small" @click="toBatchDelete">批量删除</el-button>
        </div>
        <!--表格区域 -->
        {{ids}}
        <el-table :data="categorys" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="编号" width="180"></el-table-column>
            <el-table-column prop="name" label="名称" width="180"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            
            <el-table-column fixed="right" label="操作" align="center" width="150"> 
                <template slot-scope="scope">
                <el-button @click="toEdit(scope.row)" type="text" size="small">修改</el-button>
                <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--模态框 -->
        <el-dialog title="新增栏目信息" :visible.sync="dialogFormVisible">
            {{form}}
            <el-form :model="form">
                <el-form-item label="栏目名称" label-width="80px">
                <el-input v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>

                <el-form-item label="所属栏目" label-width="80px">
                    <el-select clearable v-model="form.parentId" placeholder="请选择所属栏目">
                    <el-option v-for="c in categorys" :key="c.id" :label="c.name"
                    :value="c.id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <el-form :model="form">
                <el-form-item label="栏名描述" label-width="80px">
                <el-input type="textarea" v-model="form.description" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogFormVisible = false">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandle">确 定</el-button>
            </div>
        </el-dialog>
        <!--/模态框 -->
    </div>
</template>

<script>
import request from '@/utils/request'
import qs from 'querystring'
import { type } from 'os';
export default {
    data(){
        return{
            dialogFormVisible:false,
            categorys:[],
            form:{},
            ids:{}
        }   
        
    },
    // 生命周期
    created(){
        this.relodeData();
    },
    //事件处理函数
    methods:{
        handleSelectionChange(val){
            this.ids=val.map(item=>item.id);


        },
        toBatchDelete(){
            let url ='/category/batchDelete'
            request.post(url,this.ids)
            .then(response=>{
                this.$message({
                    message:response.message,
                    type:"success"
                })
                //重新加载
                this.relodeData();
            })
            
        },
        submitHandle(){
            //alert(qs.stringify(this.form));
            request.request({
                url:'/category/saveAndUpdate',
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
        relodeData(){
            //调用ajax查询所有文章数据
            let url ="/category/findAll"
            request.get(url)
            .then(result=>{
            this.categorys=result.data;
        })
        },
        
        toEdit(record){
            //console.log(record)
            //拿到数据弹出模态框
            this.dialogFormVisible=true;
            this.form=record;
            
        },
        toAdd(){
            //先把form中的值置空
            this.form={};
            //点击按钮弹出模态框
            this.dialogFormVisible=true
        },
        toDelete(id){
            //将id传到后端删除
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //交互
                let url ="/category/deleteById"
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

        }
    }
}
</script>

<style scoped>


</style>
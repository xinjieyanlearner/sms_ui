<template>
    <div class="artile_editor">
        <el-button type="text" @click="back">返回</el-button>
        {{form}}
        {{categorys}}
        <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="所属栏目">
            <el-select clearable v-model="form.categoryId" placeholder="请选择所属栏目">
            <el-option v-for="c in categorys" :key="c.id" :label="c.name"  :value="c.id"></el-option>
            </el-select>
        </el-form-item>

        <el-form-item label="文章标题">
            <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="正文">
            <el-input type="textarea"  rows="10" v-model="form.content"></el-input>
        </el-form-item>
        

        <el-form-item >
            <el-button type="primary" @click="onSubmit">发布</el-button>
        </el-form-item>
        </el-form>

    </div>
</template>
<script>
import qs from 'querystring'
import request from '@/utils/request'
import data from '../../views/pdf/content';
export default{
    //为模板提供数据
    data(){
        return {
            form:{},
            categorys:[]
        }
    },
    created(){
       //alert(JSON.stringify(this.$route.query));
       //双向数据绑定，将query中的数据绑定到from
       this.form=this.$route.query;
       this.loadCategorys(); 
    },
    methods:{
        //查询catraorys信息
        loadCategorys(){
            //调用ajax查询所有文章数据
            let url ="/category/findAll"
            request.get(url)
            .then(result=>{
            this.categorys=result.data;
        })
        }, 
        back(){
            this.$router.go(-1);
        },
        onSubmit(){
            //alert(JSON.stringify(this.form))
            //alert(qs.stringify(this.form))  day4 0115
            //通过ajax将前端收集的this.form发送给服务器接口
             let url ="/article/saveAndUpdate";
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
        }
    }
    
    }
</script>

<template>
    <div class="articel_list">
        <!--按钮 -->
        <div class="btns">
            <el-button type="primary" size="small"
            @click="toPublishArticle">发布文章</el-button>
        </div>
        <!--表格 -->
        <el-table :data="articles" style="width: 100%">
            <el-table-column prop="id" label="编号" width="180"></el-table-column>
            <el-table-column prop="title" label="标题" width="180"></el-table-column>
            <el-table-column prop="authorId" label="作者"></el-table-column>
            <el-table-column prop="category.name" label="所属栏目"></el-table-column>
            
            <el-table-column fixed="right" label="操作" align="center" width="150"> 
                <template slot-scope="scope">
                <el-button @click="toReview(scope.row)" type="text" size="small">查看</el-button>
                <el-button @click="toEdit(scope.row)" type="text" size="small">修改</el-button>
                <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--分页 -->
    </div>
</template>

<script>
import request from '@/utils/request'
export default {
    data(){
        return{
            articles:[]
        }   
        
    },
    // 生命周期
    created(){
        this.relodeData();
    },
    //事件处理函数
    methods:{
        relodeData(){
            //调用ajax查询所有文章数据
            let url ="http://localhost:6231/article/findArticleAndCategoryAndComments"
            request.get(url)
            .then(result=>{
            this.articles=result.data;
        })
        },
        toReview(){

        },
        toEdit(record){
            //console.log(record)
             //this.form={};
            //拿到数据跳转页面
            this.$router.push({path:'/article/editor',query:record});
        },
        toPublishArticle(){
            //跳转到编辑页面
            this.$router.push({path:'/article/editor'});
        },
        toDelete(id){
            //将id传到后端删除
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //交互
                let url ="http://localhost:6231/article/deleteById"
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
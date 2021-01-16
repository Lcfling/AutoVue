<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 基础表格
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button
                    type="primary"
                    icon="el-icon-delete"
                    class="handle-del mr10"
                    @click="delAllSelection"
                >批量删除</el-button>
                <el-select v-model="query.address" placeholder="地址" class="handle-select mr10">
                    <el-option key="1" label="广东省" value="广东省"></el-option>
                    <el-option key="2" label="湖南省" value="湖南省"></el-option>
                </el-select>
                <el-input v-model="query.name" placeholder="用户名" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
            </div>
            <el-table
                :data="res"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change="handleSelectionChange"
            >
                <el-table-column type="selection" width="55" align="center"></el-table-column>
                <el-table-column prop="Id" label="ID" width="55" align="center"></el-table-column>
                <el-table-column prop="Name" label="用户名"></el-table-column>

                <el-table-column prop="Platform" label="平台代码"></el-table-column>
                <el-table-column prop="Url" label="地址"></el-table-column>

                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                                type="text"
                                icon="el-icon-edit"
                                @click="LoginPlatform(scope.row)"
                        >登录</el-button>
                        <el-button
                                type="text"
                                icon="el-icon-edit"
                                @click="toPlan(scope.row)"
                        >计划</el-button>
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.$index, scope.row)"
                        >编辑</el-button>
                        <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.$index, scope.row)"
                        >删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="验证码">
                    <el-input v-model="form.code">
                    </el-input>
                </el-form-item>
                <el-image
                        :src="url"
                        :fit="fits"></el-image>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="HandelLogin">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { fetchData } from '../../api/index';
export default {
    name: 'basetable',
    data() {
        return {
            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
            multipleSelection: [],
            delList: [],
            editVisible: false,
            pageTotal: 0,
            form: {},
            idx: -1,
            id: -1,
            res:[],
            url:"",
            fits: ['fill', 'contain', 'cover', 'none', 'scale-down'],
            platform:""
        };
    },
    created() {
        this.getData();
    },
    methods: {

        getData() {
            this.axios.get('http://127.0.0.1:8077/game/platform').then((res)=>{
                console.log(res.data)
                //将adminId保存进session

                this.res=res.data.data
                //sessionStorage.setItem('adminId',res.data[0][0])
                //this.$router.push({path:'/'})
            }).catch(function (error) {
                console.log(error)
            });
        },
        // 获取 easy-mock 的模拟数据
        /*getData() {
            fetchData(this.query).then(res => {
                console.log(res);
                this.tableData = res.list;
                this.pageTotal = res.pageTotal || 50;
            });
        },*/


        // 触发搜索按钮
        handleSearch() {
            this.$set(this.query, 'pageIndex', 1);
            this.getData();
        },
        // 删除操作
        handleDelete(index, row) {
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
                .then(() => {
                    this.$message.success('删除成功');
                    this.tableData.splice(index, 1);
                })
                .catch(() => {});
        },
        // 多选操作
        handleSelectionChange(val) {
            this.multipleSelection = val;
        },
        delAllSelection() {
            const length = this.multipleSelection.length;
            let str = '';
            this.delList = this.delList.concat(this.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += this.multipleSelection[i].name + ' ';
            }
            this.$message.error(`删除了${str}`);
            this.multipleSelection = [];
        },
        toPlan(row){
            this.$router.push("/plan/"+row.Id)
        },
        LoginPlatform(row){


            this.platform=row.Platform
        },
        HandelLogin(){
            let params = new URLSearchParams()
            params.append("username",this.form.username)
            params.append("password",this.form.password)
            params.append("code",this.form.code)
            params.append("platform",this.platform)
            this.axios.post('http://127.0.0.1:8077/game/login',params).then((res)=>{
                console.log(res.data)
                //将adminId保存进session
                if(res.data.code){
                    this.$message.success(res.data.info);


                }else{
                    this.$message.error(res.data.info);
                }
                //sessionStorage.setItem('adminId',res.data[0][0])
                //this.$router.push({path:'/'})
            }).catch(function (error) {
                console.log(error)
            });
        },
        // 编辑操作
        handleEdit(index, row) {
            console.log("ssss",index,row)

            this.url="http://127.0.0.1:8077/getCode?platform=pingguo"
            //this.idx = index;
            //this.form = row;
            this.platform=row.Platform
            this.editVisible = true;
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;
            this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            this.$set(this.tableData, this.idx, this.form);
        },
        // 分页导航
        handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            this.getData();
        }
    }
};
</script>

<style scoped>
.handle-box {
    margin-bottom: 20px;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
</style>

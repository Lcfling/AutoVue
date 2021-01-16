<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-calendar"></i> 表单
                </el-breadcrumb-item>
                <el-breadcrumb-item>基本表单</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="80px">
                    <el-form-item label="表单名称">
                        <el-input v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="游戏选择">
                        <el-select v-model="form.gameId" @change="getPlaylist($event)" placeholder="请选择">
                            <el-option v-for="i in gamename" :key="i.code" :label="i.name" :value="i.id"></el-option>

                        </el-select>
                    </el-form-item>
                    <el-form-item label="玩法选择">
                        <el-select v-model="form.playId" placeholder="请选择">
                            <el-option v-for="i in playmethd" :key="i.id" :label="i.Name" :value="i.Playid"><span>{{i.Name}} 赔率{{i.Betfree}}</span></el-option>

                        </el-select>
                    </el-form-item>


                    <el-form-item label="初始金额设置">
                        <el-input v-model="form.money"></el-input>
                    </el-form-item>


                    <el-form-item label="递增倍率">
                        <el-input v-model="form.add"></el-input>
                    </el-form-item>
                    <el-form-item label="递增次数">
                        <el-input v-model="form.maxadd"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">表单提交</el-button>
                        <el-button>取消</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'baseform',
    data() {
        return {
            platform:{name:"pingguo"},
            gamename:[{id:"1"},{id:"2"}],
            playmethd:[],
            form: {
                name: '',
                gameId: '',
                playId: '',
                money: '',
                maxadd:'',
                add: '',
            }
        };
    },
    created() {
        this.initData()
    },
    methods: {
        initData(){
            console.log(this.param)
            this.axios.get('http://127.0.0.1:8077/game/gamelist?platform='+this.platform.name).then((res)=>{
                console.log(res.data)
                //将adminId保存进session
                this.gamename=res.data.data

                this.res=res.data.data
                //sessionStorage.setItem('adminId',res.data[0][0])
                //this.$router.push({path:'/'})
            }).catch(function (error) {
                console.log(error)
            });
        },
        getPlaylist:function(e){
            console.log(e)
            console.log(this.form)
            if (e>0){
                let params = {}
                params.platform=this.platform.name
                params.gameid=e
                this.axios.get('http://127.0.0.1:8077/game/playlist',{
                    params:params
                }).then((res)=>{
                    console.log(res.data)
                    //将adminId保存进session
                    this.playmethd=res.data.data

                    //this.res=res.data.data
                    //sessionStorage.setItem('adminId',res.data[0][0])
                    //this.$router.push({path:'/'})
                }).catch(function (error) {
                    console.log(error)
                });
            }

        },
        onSubmit() {
            this.$message.success('提交成功！');
        }
    }
};
</script>
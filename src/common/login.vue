<template>
    <div class="login-wrap">
        <div class="ms-title">後台管理系统</div>
        <div class="ms-login">
            <el-form  :model="ruleForm" :rules="rules" ref="ruleForm" label-width="0px" class="demo-ruleForm">
                <el-form-item prop="username">
                    <el-input v-model="ruleForm.username" placeholder="username"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input type="password" placeholder="password" v-model="ruleForm.password"   @keyup.enter.native="submitForm"></el-input>
                </el-form-item>
                <div class="login-btn">
                    <el-button  :loading="loading" type="primary" @click="submitForm">登陸</el-button>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
    export default {
        data: function(){
            return {
                ruleForm: {
                    username: '',
                    password: ''
                },
                loading:false,
                rules: {
                    username: [
                        { required: true, message: '請輸入帳號', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '請輸入密碼', trigger: 'blur' }
                    ]
                }
            }
        },
        methods: {
            submitForm() {
                this.$refs['ruleForm'].validate((valid) => {
                    if (valid) {    
                        this.loading = true;
                        axios({
                            url:'auth/login',
                            method:'post',
                            data:_.cloneDeep(this.ruleForm)
                        }).then((res)=>{     
                            this.loading = false;
                            if(!res.success){    
                                this.$message.error('密碼錯誤');
                            }else{
                                let date = new Date();
                                let getTime = date.getTime()+3*(864e+5);
                                date.setTime(getTime);
                                document.cookie=`token=${res.token};path=/;expires=${date.toUTCString()}`;
                                document.cookie=`username=${this.ruleForm.username};path=/;expires=${date.toUTCString()}`;
                                this.$router.push('/sku');
                            }   
                        }).catch((res)=>{
                            this.loading = false;
                             this.$router.push('/login');
                        })
                    }
                });
            }
        }
    }
</script>

<style scoped>
    .login-wrap{
        position: relative;
        width:100%;
        height:100%;
    }
    .ms-title{
        position: absolute;
        top:50%;
        width:100%;
        margin-top: -230px;
        text-align: center;
        font-size:30px;
        color: #fff;

    }
    .ms-login{
        position: absolute;
        left:50%;
        top:50%;
        width:300px;
        height:160px;
        margin:-150px 0 0 -190px;
        padding:40px;
        border-radius: 5px;
        background: #fff;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:100%;
        height:36px;
    }
</style>
<script setup>
import { User, Lock } from '@element-plus/icons-vue'
import { ref } from 'vue'
import { ElMessage } from 'element-plus'
//控制注册与登录表单的显示， 默认显示注册
const isRegister = ref(false)
//定义数据模型
const registerDate=ref({
    username:'',
    password:'',
    repassword:''
})
//定义校验密码的函数
const checkRepassword=(rule,value,callback)=>{
    if(value===''){
        callback(new Error('请再次输入密码'))
    }
    else if(value!==registerDate.value.password){
        callback(new Error('两次输入密码不一致'))
    }
    else{
        callback()
    }
}

//定义表单校验规则
const rules=ref({
    username:[
        {required:true,message:'请输入用户名',trigger:'blur'},
        {min:5,max:16,message:'长度为5-16位非空字符',trigger:'blur'}
    ],
    password:[
        {required:true,message:'请输入密码',trigger:'blur'},
        {min:5,max:16,message:'长度为5-16位非空字符',trigger:'blur'}
    ],
    repassword:[
        {validator: checkRepassword,trigger:'blur'}
    ]
})

//调用后台接口，完成注册
import {userRegisterService,userLoginService} from '@/api/user.js'
const register=async()=>{
    let result=await userRegisterService(registerDate.value)
    // if (result.code===0){
    //     alert(result.msg ? result.msg : '注册成功')
    // }
    // else{
    //     alert('注册失败')
    // }
    // alert(result.msg ? result.msg : '注册成功')
    ElMessage.success(result.msg ? result.msg : '注册成功')
}
import { useTokenStore } from '@/stores/token.js'
import { useRouter } from 'vue-router'
const router=useRouter()
const tokenStore=useTokenStore()
const login=async()=>{
    let result=await userLoginService(registerDate.value)
    // if (result.code===0){
    //     alert(result.msg ? result.msg : '登录成功')
    // }
    // else{
    //     alert('登录失败')
    // }
    // alert(result.msg ? result.msg : '登录成功')
    ElMessage.success(result.msg ? result.msg : '登录成功')
    tokenStore.setToken(result.data)
    router.push('/')
}
const clearRegisterData=()=>{
    registerDate.value={
        username:'',
        password:'',
        repassword:''
    }    
}
</script>

<template>
    <el-row class="login-page">
        <el-col :span="12" class="bg"></el-col>
        <el-col :span="6" :offset="3" class="form">
            <!-- 注册表单 -->
            <el-form ref="form" size="large" autocomplete="off" v-if="isRegister" :model="registerDate" :rules="rules">
                <el-form-item>
                    <h1>注册</h1>
                </el-form-item>
                <el-form-item prop="username">
                    <el-input :prefix-icon="User" placeholder="请输入用户名" v-model="registerDate.username"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input :prefix-icon="Lock" type="password" placeholder="请输入密码" v-model="registerDate.password"></el-input>
                </el-form-item>
                <el-form-item prop="repassword">
                    <el-input :prefix-icon="Lock" type="password" placeholder="请输入再次密码" v-model="registerDate.repassword"></el-input>
                </el-form-item>
                <!-- 注册按钮 -->
                <el-form-item>
                    <el-button class="button" type="primary" auto-insert-space @click="register">
                        注册
                    </el-button>
                </el-form-item>
                <el-form-item class="flex">
                    <el-link type="info" :underline="false" @click="isRegister = false;clearRegisterData()">
                        ← 返回
                    </el-link>
                </el-form-item>
            </el-form>
            <!-- 登录表单 -->
            <el-form ref="form" size="large" autocomplete="off" v-else :model="registerDate" :rules="rules">
                <el-form-item>
                    <h1>登录</h1>
                </el-form-item>
                <el-form-item prop="username">
                    <el-input :prefix-icon="User" placeholder="请输入用户名" v-model="registerDate.username"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input name="password" :prefix-icon="Lock" type="password" placeholder="请输入密码" v-model="registerDate.password"></el-input>
                </el-form-item>
                <el-form-item class="flex">
                    <div class="flex">
                        <el-checkbox>记住我</el-checkbox>
                        <el-link type="primary" :underline="false">忘记密码？</el-link>
                    </div>
                </el-form-item>
                <!-- 登录按钮 -->
                <el-form-item>
                    <el-button class="button" type="primary" auto-insert-space @click="login">登录</el-button>
                </el-form-item>
                <el-form-item class="flex">
                    <el-link type="info" :underline="false" @click="isRegister = true;clearRegisterData()">
                        注册 →
                    </el-link>
                </el-form-item>
            </el-form>
        </el-col>
    </el-row>
</template>

<style lang="scss" scoped>
/* 样式 */
.login-page {
    height: 100vh;
    background-color: #fff;

    .bg {
        background: url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
            url('@/assets/login_bg.jpg') no-repeat center / cover;
        border-radius: 0 20px 20px 0;
    }

    .form {
        display: flex;
        flex-direction: column;
        justify-content: center;
        user-select: none;

        .title {
            margin: 0 auto;
        }

        .button {
            width: 100%;
        }

        .flex {
            width: 100%;
            display: flex;
            justify-content: space-between;
        }
    }
}
</style>
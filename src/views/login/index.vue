<template>
    <el-row class="login-container" justify="center" :align="'middle'">
        <el-card style="max-width: 480px">
            <template #header>
                <div class="card-header">
                    <img :src="imgUrl" alt="">
                </div>
            </template>
            <div class="jump-link">
                <el-link type="primary" @click="handleChange">{{ formType ? '返回登录' : '注册账号' }}</el-link>
            </div>
            <el-form 
                :model="loginForm" 
                style="max-width: 600px"
                class="demo-ruleForm">
                <el-form-item prop="userName">
                    <el-input v-model="loginForm.userName" placeholder="手机号" :prefix-icon="UserFilled"></el-input>
                </el-form-item>
                <el-form-item prop="passWord">
                    <el-input v-model="loginForm.passWord" type="password" placeholder="密码" :prefix-icon="Lock"></el-input>
                </el-form-item>
                <el-form-item v-if="formType" prop="validCode">
                    <el-input v-model="loginForm.validCode" placeholder="验证码" :prefix-icon="Lock">
                        <template #append>
                            <span @click="countdownChange">{{ countdown.validText }}</span>
                        </template>
                    </el-input>
                    
                </el-form-item>
            </el-form>
        </el-card>
    </el-row>
</template>

<script setup>
import { UserFilled } from '@element-plus/icons-vue';
import { ref, reactive } from 'vue';
const imgUrl = new URL('../../../public/login-head.png', import.meta.url).href

// 表单数据
const loginForm = reactive({
    userName: '',
    passWord: '',
    validCode: ''
})

// 切换表单 (0登录 1注册)
const formType = ref(0)
// 点击切换登录和注册
const handleChange = () => {
    formType.value = formType.value ? 0 : 1
}

// 发送短信
const countdown = reactive({
    validText: '获取验证码',
    time: 60
})

let flag = false
let intervalId = null

const countdownChange = () => {
    // 如果已发送不处理
    if (flag) return
    
    // 密码正则
    // /^[a-zA-Z0-9_-]{4,16}$/

    // 判断手机号是否正确
    // 手机号正则
    const phoneReg = /^1(3[0-9]|4[01456879]|5[0-35-9]|6[2567]|7[0-8]|8[0-9]|9[0-35-9])\d{8}$/ 
    if (!loginForm.userName || !phoneReg.test(loginForm.userName)) {
        ElMessage({
            message: '请检查手机号是否正确!',
            type: 'warning',
        })
        return
    }
    // 倒计时
    flag = true
    intervalId = setInterval(() => {
        if (countdown.time <= 0) {
            countdown.time = 60
            countdown.validText = '获取验证码'
            clearInterval(intervalId) //清楚定时器
            flag = false
        } else {
            countdown.time -= 1
            countdown.validText = `剩余${countdown.time}秒`
        }
    }, 1000)
}
</script>

<style lang="less" scoped>
:deep(.el-card__header) {
    padding: 0
}

.login-container {
    height: 100%;

    .card-header {
        background-color: #899fe1;

        img {
            width: 430px;
        }
    }

    .jump-link {
        text-align: right;
        margin-bottom: 10px;
    }
}
</style>
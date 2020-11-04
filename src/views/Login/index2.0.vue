<template>
    <div id="login">
        <div class="login-wrap">
            <ul class="menu-tab">
                <li :class="{'current': item.current}" v-for="item in menuTab" :key="item.id" @click="toggleMenu(item)">{{item.txt}}</li>
            </ul>
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size=medium>
                <el-form-item prop="email" class="item-form">
                    <label>邮箱</label>
                    <el-input type="text" v-model="ruleForm.email" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item prop="password" class="item-form">
                    <label>密码</label>
                    <el-input type="text" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20" ></el-input>
                </el-form-item>
                <el-form-item prop="rePassword" class="item-form" v-if="model === 'register'">
                    <label>重复密码</label>
                    <el-input type="text" v-model="ruleForm.rePassword" autocomplete="off" minlength="6" maxlength="20" ></el-input>
                </el-form-item>
                <el-form-item prop="code" class="item-form">
                    <label>验证码</label>
                    <el-row :gutter="11">
                        <el-col :span="15">
                            <el-input v-model.number="ruleForm.code" minlength="6" maxlength="6" ></el-input>
                        </el-col>
                        <el-col :span="9">
                            <el-button type="success" class="block">获取验证码</el-button>
                        </el-col>
                    </el-row>
                </el-form-item>
                <el-form-item>
                    <el-button type="danger" @click="submitForm('ruleForm')" class="login-btn block">注册</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>
<script>
import { stripscript, checkEmail, checkPassword, checkCode } from '@/utils/validate'
export default {
    name: "Login",
    components: {},
    data(){
        //验证邮箱
        var validateEmail = (rule, value, callback) => {
            if (value === '') {
                callback(new Error('请输入邮箱'));
            } else if(checkEmail(value)){
                callback(new Error('邮箱格式不正确'));
            } else {
                callback();
            }
        };
        //验证密码
        var validatePassword = (rule, value, callback) => {
            //过滤后的数据
            this.ruleForm.password = stripscript(value);
            value = this.ruleForm.password;

            if (value === '') {
                callback(new Error('请输入密码'));
            } else if (checkPassword(value)) {
                callback(new Error('密码为6-20位，数字+字母'));
            } else {
                callback();
            }
        };
        //验证重复密码
        var validateRePassword = (rule, value, callback) => {
            //如果模块值为login，直接通过
            if(this.model == 'login'){ callback(); }

            //过滤后的数据
            this.ruleForm.rePassword = stripscript(value);
            value = this.ruleForm.rePassword;

            if (value === '') {
                callback(new Error('请再次输入密码'));
            } else if (value != this.ruleForm.password) {
                callback(new Error('两次密码不一致'));
            } else {
                callback();
            }
        };
        //验证验证码
        var validateCode = (rule, value, callback) => {
            if (!value) {
                return callback(new Error('验证码不能为空'));
            }else if(checkCode(value)){
                return callback(new Error('验证码为6位，数字+字母'));
            }else {
                return callback();
            }
            // setTimeout(() => {
            //     if (!Number.isInteger(value)) {
            //         callback(new Error('请输入数字值'));
            //     } else {
            //         if (value < 18) {
            //             callback(new Error('必须年满18岁'));
            //     } else {
            //         callback();
            //     }
            // }
            // }, 1000);
        };
        return {
            menuTab: [
                {txt: '登录', current:true, type: 'login'},
                {txt: '注册', current:false, type: 'register'}
            ],
            //模块
            model: 'login',
            //表单数据
            ruleForm: {
                email: '',
                password: '',
                rePassword: '',
                code: ''
            },
            rules: {
                email: [
                    { validator: validateEmail, trigger: 'blur' }
                ],
                password: [
                    { validator: validatePassword, trigger: 'blur' }
                ],
                rePassword: [
                    { validator: validateRePassword, trigger: 'blur' }
                ],
                code: [
                    { validator: validateCode, trigger: 'blur' }
                ]
            }
        }
    },
    created(){},
    //挂载完成，自动执行
    mounted(){},
    //写函数的地方
    methods: {
        toggleMenu(data){
            this.menuTab.forEach((element,index) => {
                element.current = false;
            });
            data.current = true;
            //修改模块的值
            this.model = data.type;
        },
        submitForm(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    alert('submit!');
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });
        }
    }
}
</script>
<style lang="scss" scoped>
    #login {
        height: 100vh;
        background-color: #344a5f;
    }
    .login-wrap {
        width: 330px;
        margin: auto;
    }
    .menu-tab {
        text-align: center;
        li {
        display: inline-block;
        width: 88px;
        line-height: 36px;
        font-size: 14px;
        color: #fff;
        border-radius: 2px;
        cursor: pointer;
        }
        .current {
            background-color: rgba(0,0,0,.1);
        }
    }
    .login-form {
        margin-top: 29px;
        label {
            display: block;
            font-size: 14px;
            color: #fff;
            margin-bottom: 3px; 
            text-align: left;
        }
        .item-form {
            margin-bottom: 13px;
        }
        .block {
            display: block;
            width: 100%;
        }
        .login-btn {
            margin-top: 19px;
        }
    }
    
</style>

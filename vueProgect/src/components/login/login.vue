<template>
    <div id="loginIL09">
        <div class="iconTitle">
            <img src="../../static/imgs/softIcon.png">
            <div>智能在线学习平台
            </div>
        </div>
        <el-form class="loginForm"
                 v-if="ifLogin==='登录'"
                 :model="formDataLogin"
                 :rules="rulesLogin"
                 ref="loginForm">
            <el-form-item prop="account"
                          key="account">
                <el-input
                        prefix-icon="fa fa-user"
                        placeholder="请输入账号"
                        class="pass"
                        v-model="formDataLogin.account">
                    <span slot="suffix" class="forget" @click="ifLogin='注册'">注册账号</span>
                </el-input>
            </el-form-item>
            <el-form-item prop="passwd"
                          key="passwd">
                <el-input
                        prefix-icon="fa fa-lock"
                        type="password"
                        placeholder="请输入密码"
                        class="pass"
                        v-model="formDataLogin.passwd">
                    <span slot="suffix" class="forget" @click="dialogFormVisible = true">忘记密码</span>
                </el-input>
            </el-form-item>
            <el-form-item style="margin-bottom: 10px">
                <el-button
                        type="primary"
                        class="submit"
                        @click="submitForm('loginForm')">
                    {{ifLogin}}
                </el-button>
            </el-form-item>
            <find-pass :dialogFormVisible.sync="dialogFormVisible"></find-pass>
        </el-form>
        <el-form class="loginForm"
                 style="width: 336px"
                 v-if="ifLogin==='注册'"
                 :model="formDataSign"
                 :rules="rulesSign"
                 label-width="80px"
                 ref="formSign">
            <el-form-item prop="accountLogin"
                          key="accountLogin"
                          label="账号">
                <el-input
                        placeholder="请输入账号"
                        v-model="formDataSign.accountLogin">
                </el-input>
            </el-form-item>

            <el-form-item prop="name"
                          key="name"
                          label="真实姓名">
                <el-input
                        placeholder="请输入真实姓名"
                        v-model="formDataSign.name">
                </el-input>
            </el-form-item>

            <el-form-item prop="areaFocus"
                          key="areaFocus"
                          label="专注领域">
                <el-select v-model="formDataSign.areaFocus"
                           multiple
                           placeholder="请选择专注领域">
                    <el-option v-for="item in filterType('全部')"
                               :key="item"
                               :label="item"
                               :value="item">
                    </el-option>
                </el-select>
            </el-form-item>

            <el-form-item prop="eMail"
                          key="eMail"
                          label="邮箱">
                <el-input
                        placeholder="请输入邮箱"
                        v-model="formDataSign.eMail">
                </el-input>
            </el-form-item>

            <el-form-item prop="passwdLogin"
                          key="passwdLogin"
                          label="密码">
                <el-input
                        type="password"
                        placeholder="请输入密码"
                        v-model="formDataSign.passwdLogin">
                </el-input>
            </el-form-item>
            <el-form-item prop="checkPasswd"
                          key="checkPasswd"
                          label="确认密码">
                <el-input
                        type="password"
                        placeholder="请再次输入密码"
                        v-model="formDataSign.checkPasswd">
                </el-input>
            </el-form-item>
            <el-form-item style="margin-bottom: 10px">
                <el-button
                        type="primary"
                        class="submit"
                        @click="submitForm('formSign')">
                    {{ifLogin}}
                </el-button>
            </el-form-item>
            <div class="hasAcc" @click="ifLogin='登录'">
                已有账号
            </div>
        </el-form>
    </div>
</template>

<script>
    import findPass from './findPasswd.vue';
    import sha256 from 'sha256';
    import {mapGetters} from 'vuex'
    import {mapMutations} from 'vuex'

    export default {
        data() {
            //自定义的验证在表单前面不会有红色的星星
            //登录账号的验证
            let validateAcco = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入账号'));
                }
                callback();
            };
            //注册账号是否重复的验证
            let validateAccoRe = (rule, value, callback) => {
                this.$ajaxJava.post('accountCheck',{account:value}).then((res)=>{
                    if(res.data) {
                        callback(new Error('你输入的账号已经存在'));
                    }else{
                        callback();
                    }
                });
            };
            //密码的验证
            let validatePass = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入密码'));
                }
                callback();
            };
            //密码再次确认
            let validateCheckPass = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请再次输入密码'));
                } else if (value !== this.formDataSign.passwdLogin) {
                    callback(new Error('两次输入密码不一致!'));
                } else {
                    callback();
                }
            };
            return {
                //密码寻回弹出框
                dialogFormVisible: false,
                //登录表单的数据
                formDataLogin: {
                    account: '',
                    passwd: ''
                },
                //注册表单的数据
                formDataSign: {
                    //昵称，账号
                    accountLogin: '',
                    name: '',
                    areaFocus: [],
                    eMail: '',
                    passwdLogin: '',
                    checkPasswd: '',
                },
                //登录的验证规则
                rulesLogin: {
                    account: [
                        {validator: validateAcco, trigger: 'change'}

                    ],
                    passwd: [
                        {validator: validatePass, trigger: 'change'}
                    ],
                },
                //注册的验证规则
                rulesSign: {
                    accountLogin: [
                        {required: true, message: '请输入账号', trigger: 'change'},
                        {validator: validateAccoRe, trigger: 'blur'}
                    ],
                    name: [
                        {required: true, message: '请输入真实姓名', trigger: 'change'}
                    ],

                    areaFocus: [
                        {required: true, message: '请选择专注领域', trigger: 'change'}
                    ],

                    eMail: [
                        {required: true, message: '请输入邮箱地址', trigger: 'change'},
                        {type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur'}
                    ],
                    passwdLogin: [
                        {required: true, message: '请输入密码', trigger: 'change'}
                    ],
                    checkPasswd: [
                        {required: true, message: '请再次输入密码', trigger: 'change'},
                        {validator: validateCheckPass, trigger: 'blur'}
                    ]
                },
                //注册还是登录
                ifLogin: '登录'
            }
        },
        computed: {
            ...mapGetters(['filterType']),
        },
        methods: {
            /**
             * 提交
             * @param formName
             */
            submitForm(formName) {
                //对整个表单进行校验
                this.$refs[formName].validate((valid) => {
                    //如果没有错误，则提交数据
                    if (valid) {
                        //不同的数据封装
                        let data;
                        //登录
                        if (formName === 'loginForm') {
                            data = {
                                account: this.formDataLogin.account,
                                password: sha256(this.formDataLogin.passwd)
                            };
                            this.$ajaxJava.post('login', data).then((response) => {
                                //如果返回的是0
                                if (!response.data.accountHashMap) {
                                    this.$message({
                                        type: 'error',
                                        message: `账号或者密码错误`
                                    });
                                } else {//登录成功
                                    this.successhandle(response.data);
                                }
                            });
                        } else {//注册
                            data = {
                                account: this.formDataSign.accountLogin,
                                //把数组切割成字符串
                                selfInformation: {
                                    position: this.formDataSign.areaFocus.join('/'),
                                    email: this.formDataSign.eMail,
                                    trueName: this.formDataSign.name,
                                },
                                password: sha256(this.formDataSign.passwdLogin)
                            };
                            this.$ajaxJava.post('register', data).then((response) => {
                                //不知道的错误
                                if (!response.data) {
                                    this.$message({
                                        type: 'error',
                                        message: '注册失败'
                                    });
                                } else {//注册成功
                                    this.successhandle(response.data);
                                }
                            });
                        }
                    } else {//表格数据错误
                        return false;
                    }
                });
            },

            /**
             *设置返回的用户数据
             */
            ...mapMutations('info', [
                'setAccount',
                'setAvatarUrl',
                'setAreaFocus',
                'setRole'
            ]),

            /**
             *当成功登录或者注册时的处理
             */
            successhandle(data) {
                //作为下次不用登录的依据
                localStorage["ifLogin"] = data.accountHashMap;
                //初始化页面信息
                this.setAccount(data.account);
                this.setAvatarUrl(data.avatarUrl);
                this.setAreaFocus(data.areaFocus);
                this.setRole(data.role);
                //跳转页面
                this.$router.push({path: '/main/recommend'});
            }
        },
        components: {
            findPass
        }
    }
</script>

<style lang="scss">
    $primaryBlue: #409EFF;
    #loginIL09 {
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background-image:url("../../static/imgs/background.jpg");
        .iconTitle{
            display: flex;
            justify-content:center;
            align-items: center;
            div{
                color:$primaryColor;
                font-size: 18px;
                font-weight: bold;
            }
        }
        .loginForm {
            width: 330px;
            padding: 20px;
            .forget {
                font-size: 12px;
                cursor: pointer;
                &:hover {
                    color: $primaryBlue;
                }
            }
            .pass {
                .el-input__inner {
                    padding-right: 52px;
                }
            }
            .submit {
                width: 100%;
            }
        }
        .hasAcc {
            display: flex;
            justify-content: flex-end;
            align-items: center;
            font-size: 13px;
            color: #c0c4cc;
            cursor: pointer;
            &:hover {
                color: $primaryBlue
            }
        }
    }
</style>


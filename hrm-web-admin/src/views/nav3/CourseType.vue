<template id="courseType">
    <el-row style="height: 100%;border: 1px solid #DCDFE6;margin-top: 10px">
        <el-col :span="6" style="border-right: 1px solid #DCDFE6; min-height:500px; width: 200px;">
            <div class="grid-content bg-purple">
                <el-tree :data="courseTypes"  style="border: 0;" :props="defaultProps"  @node-click="handleNodeClick">
                    <div v-show="menuVisible">
                        <ul id="menu" class="menu">
                            <li class="menu__item">新增</li>
                            <li class="menu__item">修改</li>
                            <li class="menu__item">删除</li>
                        </ul>
                    </div>
                </el-tree>
            </div>
        </el-col>
        <el-col :span="17" style="margin-left: 10px;padding-top: 10px">
            <!--工具条-->
            <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
                <el-form :inline="true" :model="filters">
                    <el-form-item>
                        <el-input v-model="filters.name" placeholder="姓名"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" v-on:click="getUsers">查询</el-button>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="handleAdd">新增</el-button>
                    </el-form-item>
                </el-form>
            </el-col>

            <!--列表-->
            <el-table :data="users" highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;">
                <el-table-column type="selection" width="55">
                </el-table-column>
                <el-table-column type="index" width="60">
                </el-table-column>
                <el-table-column prop="name" label="姓名" width="120" sortable>
                </el-table-column>
                <el-table-column prop="sex" label="性别" width="100" :formatter="formatSex" sortable>
                </el-table-column>
                <el-table-column prop="age" label="年龄" width="100" sortable>
                </el-table-column>
                <el-table-column prop="birth" label="生日" width="120" sortable>
                </el-table-column>
                <el-table-column prop="addr" label="地址" min-width="180" sortable>
                </el-table-column>
                <el-table-column label="操作" width="150">
                    <template scope="scope">
                        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                        <el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>

            <!--工具条-->
            <el-col :span="24" class="toolbar">
                <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>
                <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
                </el-pagination>
            </el-col>

            <!--编辑界面-->
            <el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
                <el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
                    <el-form-item label="姓名" prop="name">
                        <el-input v-model="editForm.name" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="性别">
                        <el-radio-group v-model="editForm.sex">
                            <el-radio class="radio" :label="1">男</el-radio>
                            <el-radio class="radio" :label="0">女</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="年龄">
                        <el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>
                    </el-form-item>
                    <el-form-item label="生日">
                        <el-date-picker type="date" placeholder="选择日期" v-model="editForm.birth"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="地址">
                        <el-input type="textarea" v-model="editForm.addr"></el-input>
                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click.native="editFormVisible = false">取消</el-button>
                    <el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
                </div>
            </el-dialog>

            <!--新增界面-->
            <el-dialog title="新增" v-model="addFormVisible" :close-on-click-modal="false">
                <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
                    <el-form-item label="姓名" prop="name">
                        <el-input v-model="addForm.name" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="性别">
                        <el-radio-group v-model="addForm.sex">
                            <el-radio class="radio" :label="1">男</el-radio>
                            <el-radio class="radio" :label="0">女</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="年龄">
                        <el-input-number v-model="addForm.age" :min="0" :max="200"></el-input-number>
                    </el-form-item>
                    <el-form-item label="生日">
                        <el-date-picker type="date" placeholder="选择日期" v-model="addForm.birth"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="地址">
                        <el-input type="textarea" v-model="addForm.addr"></el-input>
                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click.native="addFormVisible = false">取消</el-button>
                    <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
                </div>
            </el-dialog>
        </el-col>
    </el-row>
</template>
<style>
    .el-row {
        margin-bottom: 20px;
        height: 100%;
    }
    :last-child {
        margin-bottom: 0;
    }
    #courseType el-col {
        border: 1px solid red;
        border-radius: 4px;
    }
    .grid-content {
        border-radius: 4px;
        min-height: 36px;
    }
    .menu__item {
        display: block;
        line-height: 20px;
        text-align: center;
        margin:10px;
        cursor: default;
    }
    .menu__item:hover{
        color: #FF0000;
    }

    .menu {
        height: auto;
        width: auto;
        position: absolute;
        font-size: 14px;
        text-align: left;
        border-radius: 10px;
        border: 1px solid #c1c1c1;
        background-color: #ffffff;
    }

    li:hover {
        background-color: #E0E0E2;
        color: white;
    }
</style>

<script>
    export default {
        data() {
            return {
                courseTypes:[],
                defaultProps: {
                    children: 'children',
                    label: 'name'
                },
                menuVisible:false,
                filters: {
                    name: ''
                },
                users: [],
                total: 0,
                page: 1,
                listLoading: false,
                sels: [],//列表选中列

                editFormVisible: false,//编辑界面是否显示
                editLoading: false,
                editFormRules: {
                    name: [
                        { required: true, message: '请输入姓名', trigger: 'blur' }
                    ]
                },
                //编辑界面数据
                editForm: {
                    id: 0,
                    name: '',
                    sex: -1,
                    age: 0,
                    birth: '',
                    addr: ''
                },

                addFormVisible: false,//新增界面是否显示
                addLoading: false,
                addFormRules: {
                    name: [
                        { required: true, message: '请输入姓名', trigger: 'blur' }
                    ]
                },
                //新增界面数据
                addForm: {
                    name: '',
                    sex: -1,
                    age: 0,
                    birth: '',
                    addr: ''
                }
            }
        },
        methods:{
            getTreeData(){
                // 发送一个异步请求: get请求 /product/courseType/treeData
                this.$http.get("/course/courseType/treeData").then(res=>{
                    console.log(this);
                    this.courseTypes = res.data;
                });
            },
            //右键菜单
            handleNodeClick(row, event){
                this.menuVisible = false; // 先把模态框关死，目的是 第二次或者第n次右键鼠标的时候 它默认的是true
                this.menuVisible = true; // 显示模态窗口，跳出自定义菜单栏
                var menu = document.querySelector('#menu');
                this.styleMenu(menu);
            },
            foo() {
                // 取消鼠标监听事件 菜单栏
                this.menuVisible = false;
                document.removeEventListener('click', this.foo); // 要及时关掉监听，不关掉的是一个坑，不信你试试，虽然前台显示的时候没有啥毛病，加一个alert你就知道了
            },
            styleMenu(menu) {
                if (event.clientX > 1800) {
                    menu.style.left = event.clientX - 100 + 'px';
                } else {
                    menu.style.left = event.clientX + 1 + 'px';
                }
                document.addEventListener('click', this.foo); // 给整个document新增监听鼠标事件，点击任何位置执行foo方法
                if (event.clientY > 700) {
                    menu.style.top = event.clientY - 30 + 'px';
                } else {
                    menu.style.top = event.clientY - 10 + 'px';
                }
            },
            //性别显示转换
            formatSex: function (row, column) {
                return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
            },
            handleCurrentChange(val) {
                this.page = val;
                this.getUsers();
            },
            //获取用户列表
            getUsers() {
                let para = {
                    page: this.page,
                    name: this.filters.name
                };
                this.listLoading = true;
                //NProgress.start();
                getUserListPage(para).then((res) => {
                    this.total = res.data.total;
                    this.users = res.data.users;
                    this.listLoading = false;
                    //NProgress.done();
                });
            },
            //删除
            handleDel: function (index, row) {
                this.$confirm('确认删除该记录吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    //NProgress.start();
                    let para = { id: row.id };
                    removeUser(para).then((res) => {
                        this.listLoading = false;
                        //NProgress.done();
                        this.$message({
                            message: '删除成功',
                            type: 'success'
                        });
                        this.getUsers();
                    });
                }).catch(() => {

                });
            },
            //显示编辑界面
            handleEdit: function (index, row) {
                this.editFormVisible = true;
                this.editForm = Object.assign({}, row);
            },
            //显示新增界面
            handleAdd: function () {
                this.addFormVisible = true;
                this.addForm = {
                    name: '',
                    sex: -1,
                    age: 0,
                    birth: '',
                    addr: ''
                };
            },
            //编辑
            editSubmit: function () {
                this.$refs.editForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.editLoading = true;
                            //NProgress.start();
                            let para = Object.assign({}, this.editForm);
                            para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
                            editUser(para).then((res) => {
                                this.editLoading = false;
                                //NProgress.done();
                                this.$message({
                                    message: '提交成功',
                                    type: 'success'
                                });
                                this.$refs['editForm'].resetFields();
                                this.editFormVisible = false;
                                this.getUsers();
                            });
                        });
                    }
                });
            },
            //新增
            addSubmit: function () {
                this.$refs.addForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.addLoading = true;
                            //NProgress.start();
                            let para = Object.assign({}, this.addForm);
                            para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
                            addUser(para).then((res) => {
                                this.addLoading = false;
                                //NProgress.done();
                                this.$message({
                                    message: '提交成功',
                                    type: 'success'
                                });
                                this.$refs['addForm'].resetFields();
                                this.addFormVisible = false;
                                this.getUsers();
                            });
                        });
                    }
                });
            },
            selsChange: function (sels) {
                this.sels = sels;
            },
            //批量删除
            batchRemove: function () {
                var ids = this.sels.map(item => item.id).toString();
                this.$confirm('确认删除选中记录吗？', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    //NProgress.start();
                    let para = { ids: ids };
                    batchRemoveUser(para).then((res) => {
                        this.listLoading = false;
                        //NProgress.done();
                        this.$message({
                            message: '删除成功',
                            type: 'success'
                        });
                        this.getUsers();
                    });
                }).catch(() => {

                });
            }
        },
        mounted(){
            //对courseTypes数据赋值
           this.getTreeData();
        }
    };
</script>
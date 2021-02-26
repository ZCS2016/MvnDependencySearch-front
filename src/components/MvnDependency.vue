<template>
    <div id="app">
        <!-- 过滤条件 -->
        <div class="box-filters">
            <el-form :inline="true" :model="filters" ref="filters">
                <el-row :gutter="20" style="display: flex;align-items: flex-end;">
                    <el-col :span="4">
                        <el-form-item label="项目:" prop="project">
                            <el-select v-model="filters.project" placeholder="项目" :clearable="false">
                                <el-option value="" label=""></el-option>
                            </el-select>
                        </el-form-item>
                    </el-col>
                    <el-col :span="4">
                        <el-form-item label="检测结果:" prop="checkResult">
                            <el-select v-model="filters.checkResult" placeholder="检测结果" :clearable="false">
                                <el-option value="" label="ALL"></el-option>
                                <el-option value="true" label="True"></el-option>
                                <el-option value="false" label="False"></el-option>
                            </el-select>
                        </el-form-item>
                    </el-col>

                    <el-col :span="6">
                        <el-form-item>
                            <el-button type="primary" @click="handleQuery()">查询</el-button>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="downloadExcel()">下载</el-button>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="danger" @click="resetData('filters')">重置</el-button>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="success" @click="handleSearch()">检测</el-button>
                        </el-form-item>
                    </el-col>
                </el-row>
            </el-form>
        </div>
        <el-table :data="tableData" style="width: 100%">
            <el-table-column
                    type="index"
                    width="50">
            </el-table-column>
            <el-table-column
                    prop="project"
                    label="项目"
                    width="200">
            </el-table-column>
            <el-table-column
                    prop="libraryName"
                    label="依赖"
                    width="400">
            </el-table-column>
            <el-table-column
                    prop="mvnRepositoryUrl"
                    label="MvnRepository"
                    width="300">
                <template slot-scope="scope">
                    <el-link type="primary" :href="scope.row.mvnRepositoryUrl" target="_blank">
                        {{scope.row.mvnRepositoryUrl}}
                    </el-link>
                </template>
            </el-table-column>
            <el-table-column
                    prop="dependencyXml"
                    label="Xml"
                    width="600">
                <template slot-scope="scope">
                    <el-input
                            type="textarea"
                            autosize
                            v-model="scope.row.dependencyXml">
                    </el-input>
                </template>
            </el-table-column>
            <el-table-column
                    prop="centralUrl"
                    label="CentralUrl"
                    width="300">
                <template slot-scope="scope">
                    <el-link type="primary" :href="scope.row.centralUrl" target="_blank">
                        {{scope.row.centralUrl}}
                    </el-link>
                </template>
            </el-table-column>
            <el-table-column
                    prop="repository"
                    label="Maven仓库1"
                    width="120">
                <template slot-scope="scope">
                    <el-link type="primary" :href="scope.row.repositoryCheckResultList[0].libraryUrl" target="_blank">
                        {{scope.row.repositoryCheckResultList[0].repository}}
                    </el-link>
                </template>
            </el-table-column>
            <el-table-column
                    prop="checkResult"
                    label="检测结果1"
                    width="100">
                <template slot-scope="scope">
                    <el-tag :type="scope.row.repositoryCheckResultList[0].checkResult ? 'success' : 'danger'"
                            disable-transitions>{{scope.row.repositoryCheckResultList[0].checkResult}}</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                    prop="repository"
                    label="Maven仓库2"
                    width="120">
                <template slot-scope="scope">
                    <el-link type="primary" :href="scope.row.repositoryCheckResultList[1].libraryUrl" target="_blank">
                        {{scope.row.repositoryCheckResultList[1].repository}}
                    </el-link>
                </template>
            </el-table-column>
            <el-table-column
                    prop="checkResult"
                    label="检测结果2"
                    width="100">
                <template slot-scope="scope">
                    <el-tag :type="scope.row.repositoryCheckResultList[1].checkResult ? 'success' : 'danger'"
                            disable-transitions>{{scope.row.repositoryCheckResultList[1].checkResult}}</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                    prop="updateTime"
                    label="更新时间"
                    width="240">
            </el-table-column>
        </el-table>
    </div>
</template>

<script>
    import base from '@/api/base';

    let baseUrl = base.url;
    const axios = require('axios');

    export default {
        name: 'MvnDependency',
        data: function () {
            return {
                filters: {
                    project: '',
                    checkResult: 'false'
                },
                tableData: [],
                loading: false
            }
        },
        methods: {
            //查询
            getDataList() {
                axios({
                    method: 'post',
                    url: baseUrl + '/MvnDependency/getMvnDependencyList',
                    data: {
                        project: this.filters.project,
                        checkResult: this.filters.checkResult
                    }
                }).then(res => {
                    this.tableData = res.data.mvnDependencyResultList;
                });
            },
            resetData(formName) {
                this.$refs[formName].resetFields();
            },
            //查询
            handleQuery() {
                this.getDataList();
            },
            //检测
            handleSearch() {
                axios({
                    method: 'post',
                    url: baseUrl + '/MvnDependency/handleSearch',
                    data: {
                        project: this.filters.project
                    }
                }).then(res => {
                    this.$message({
                        message: '检测完成',
                        type: 'success'
                    });
                });
            },
            //下载Excel
            downloadExcel() {
                axios({
                    method: 'post',
                    url: baseUrl + '/MvnDependency/downloadMvnDependencyExcel',
                    data: {
                        project: this.filters.project,
                        checkResult: this.filters.checkResult
                    }
                }).then(res => {
                    if (!res) {
                        this.$message.error("下载文件失败");
                        return false;
                    }
                    const blob = new Blob([res.data], {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=utf-8'});
                    const downloadElement = document.createElement('a');
                    const href = window.URL.createObjectURL(blob);
                    let contentDisposition = res.headers['content-disposition'];  //从response的headers中获取filename, 后端response.setHeader("Content-disposition", "attachment; filename=xxxx.docx") 设置的文件名;
                    let filename = 'MvnDependency.xls';
                    downloadElement.style.display = 'none';
                    downloadElement.href = href;
                    downloadElement.download = filename ; //下载后文件名
                    document.body.appendChild(downloadElement);
                    downloadElement.click(); //点击下载
                    document.body.removeChild(downloadElement); //下载完成移除元素
                    window.URL.revokeObjectURL(href); //释放掉blob对象
                });
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

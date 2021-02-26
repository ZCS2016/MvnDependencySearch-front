<template>
    <el-upload
            class="upload-demo"
            ref="upload"
            :action="fileUploadUrl"
            :file-list="fileList"
            :limit="1"
            :on-success="handleSuccess"
            :auto-upload="false">
        <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
        <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>
        <div slot="tip" class="el-upload__tip">只能上传Idea的iml文件</div>
    </el-upload>
</template>

<script>
    import base from '@/api/base';

    let baseUrl = base.url;

    export default {
        name: "FileUpload",
        data() {
            return {
                fileList: [],
                fileUploadUrl: baseUrl + '/MvnDependency/fileUpload'
            };
        },
        methods: {
            submitUpload() {
                this.$refs.upload.submit();
            },
            handleSuccess(response, file, fileList) {
                this.$message({
                    message: '上传成功',
                    type: 'success'
                });
            }
        }
    }
</script>

<style scoped>

</style>

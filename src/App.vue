<template>
  <div id="app">
    <el-tabs type="border-card">
      <el-tab-pane label="文件上传">
        <FileUpload/>
      </el-tab-pane>
      <el-tab-pane label="Maven依赖">
        <MvnDependency/>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import FileUpload from './components/FileUpload.vue';
import MvnDependency from './components/MvnDependency.vue';

export default {
  name: 'App',
  components: {
    FileUpload,
    MvnDependency
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

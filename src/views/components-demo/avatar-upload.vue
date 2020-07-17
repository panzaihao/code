<template>
  <div class="components-container">

    <pan-thumb :image="image" />

    <el-button type="primary" icon="el-icon-upload" style="position: absolute;bottom: 300px;margin-left: 200px;" @click="imagecropperShow=true">
      Change Avatar
    </el-button>

    <image-cropper
      v-show="imagecropperShow"
      :key="imagecropperKey"
      :width="300"
      :height="300"
      url="https://httpbin.org/post"
      lang-type="en"
      @close="close"
      @crop-upload-success="cropSuccess"
    />
    <el-input placeholder="姓名" v-model="input1" class="inputclass"></el-input>
    <el-input placeholder="学号" v-model="input2" class="inputclass"></el-input>
    <el-input placeholder="学校" v-model="input3" class="inputclass"></el-input>
    <el-input placeholder="电子邮箱" v-model="input4" class="inputclass"></el-input>
  </div>
</template>

<script>
import ImageCropper from '@/components/ImageCropper'
import PanThumb from '@/components/PanThumb'

export default {
  name: 'AvatarUploadDemo',
  components: { ImageCropper, PanThumb },
  data() {
    return {
      imagecropperShow: false,
      imagecropperKey: 0,
      image: 'https://wpimg.wallstcn.com/577965b9-bb9e-4e02-9f0c-095b41417191',
      input1: '',
      input2: '',
      input3: '',
      input4: ''
    }
  },
  methods: {
    cropSuccess(resData) {
      this.imagecropperShow = false
      this.imagecropperKey = this.imagecropperKey + 1
      this.image = resData.files.avatar
    },
    close() {
      this.imagecropperShow = false
    }
  }
}
</script>

<style scoped>
  .avatar{
    width: 200px;
    height: 200px;
    border-radius: 50%;
  }
  .inputclass{
    margin-bottom: 30px;
    margin-top: 10px;
  }
</style>

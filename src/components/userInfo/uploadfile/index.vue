<template>
  <div class="upload_form">
    <div class="content">
      <img src="@/assets/icons/upload.svg" alt="" />
      <div class="title_upload">Hãy kéo thả ảnh vào đây hoặc</div>
      <label for="file_upload" class="file_upload--label">nhấn vào đây</label>
      <input
        type="file"
        id="file_upload"
        class="file_upload--input"
        accept="image/*"
        @change="handleUpload($event)"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "UploadFile",
  data: function () {
    return {
      selectedFile: null,
      hover: false,
      error: {
        status: false,
        text: "",
      },
    };
  },
  methods: {
    handleUpload(e) {
      this.$emit("handleUpload",e);     
    }
  }}


</script>

<style scoped lang="scss">
.upload_form {
  text-align: center;
  height: 192px;
  margin: 0 auto;
  border: 1px solid #dcdcdc;
  border-radius: 3px;
  .content {
    margin-top: 60px;
    .title_upload {
      font-size: 20px;
    }
    label {
      text-decoration: underline;
      cursor: pointer;
    }
    input {
      display: none;
    }
  }
}
</style>
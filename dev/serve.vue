<script>
import Vue from "vue";
import VueEasyMultiFileUpload from "@/vue-easy-multi-file-upload.vue";

export default Vue.extend({
  name: "ServeDev",
  components: {
    VueEasyMultiFileUpload,
  },
  data() {
    return {
      config: {
        id: "multi-file-uploader",
        label: "Upload your files",
        maxFiles: 6,
        uploadUrl: "http://localhost:8005/api/common/assets/fd",
        deleteUrl: "http://localhost:8005/api/common/assets/",
        uploadHttpMethod: "POST",
        deleteHttpMethod: "DELETE",
        uploadFieldName: "file",
        deleteFieldName: "filePath",
        Authorization:
          "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MjI1MzA5OTQsImRhdGEiOnsicm9sZSI6ImFkbWluIn0sImlhdCI6MTYyMjQ0NDU5NH0.F8-ZWUnOxgVfmgv43xeCiYu937xnRm0mhQJfbG-piZI",
       style: {
          width: "150px",
          height: "200px",
        },
        allowExt: ["jpg", "png", "gif", "mp4", "txt"],
        fileType: "image",
        maxSize: 5,
        delimiter: "|",
      },

      values: null,
    };
  },
});
</script>

<template>
  <div id="app">
    <vue-easy-multi-file-upload :config="config" v-model="values" />
  </div>
</template>

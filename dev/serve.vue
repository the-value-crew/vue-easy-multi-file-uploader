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
      config1: {
        id: "multi-file-uploader",
        label: "Upload your files",
        maxFiles: 3,
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
        maxSize: 5,
        delimiter: "|",
      },

      config2: {
        id: "photos-uploader",
        label: `<i class="fas fa-plus-circle"></i> Upload Photo`,
        maxFiles: 6,
        uploadUrl: "/common/assets/fd",
        deleteUrl: "/common/assets/",
        Authorization: ["Authorization"],
        style: {
          width: "150px",
          height: "175px",
        },
        allowExt: ["jpg", "png", "gif", "mp4", "mov"],
        maxSize: 50,
        delimiter: "|"
      },

      values: "asdasd.sad",
    };
  },
});
</script>

<template>
  <div id="app">
    <vue-easy-multi-file-upload :config="config1" v-model="values" :value="values" />
    <hr>
    <vue-easy-multi-file-upload
      :config="config2"
      v-model="values"
      :value="values"
    />
  </div>
</template>

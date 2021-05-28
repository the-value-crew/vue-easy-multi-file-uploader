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
        // label: "Upload your files",
        maxFiles: 6,
        uploadUrl: "http://localhost:8005/api/common/assets/fd",
        deleteUrl: "http://localhost:8005/api/common/assets/",
        uploadHttpMethod: "POST",
        deleteHttpMethod: "DELETE",
        Authorization:
          "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MjIyOTM2NDQsImRhdGEiOnsicGhvbmVObyI6Iis5Nzc5ODQzNDE4MzAwIiwicm9sZSI6InVzZXIifSwiaWF0IjoxNjIyMjA3MjQ0fQ.jNhTOhjM-QRhZIWMsVXWAUDfFA-lE-HzydmknTMXF9E",
        width: "90px",
        height: "75px",
        allowExt: ["jpg", "png", "gif", "mp4"],
        fileType: "image", // image, video, other
        maxSize: 5,
        // delimiter: "|",
      },

      values: null,
    };
  },
});
</script>

<template>
  <div id="app">
    <vue-easy-multi-file-upload :config="config" />

    {{ values }}
  </div>
</template>

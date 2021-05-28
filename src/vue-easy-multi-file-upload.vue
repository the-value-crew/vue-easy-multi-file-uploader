<template>
  <div :id="config_.id">
    <input type="file" ref="fileInput" hidden @change="handleFileChange" />

    <div class="filesList">
      <div
        class="filesListItem"
        v-for="(i, index) in config_.maxFiles"
        :key="index"
        :style="fileListItemStyle"
        @click="handleFileListItemClick(index, files[index] ? false : true)"
      >
        <template v-if="files && files[index]">
          <!-- Loading -->
          <span v-if="files[index].loading">{{
            config_.uploadingMessage
          }}</span>

          <!-- image -->
          <img
            v-else-if="config_.fileType == 'image' && files[index].url"
            :src="files[index].url"
          />

          <!-- Video -->
          <video
            v-else-if="config_.fileType == 'video' && files[index].url"
            loop
            controls
          >
            <source :src="files[index].url" />
            Your browser does not support the video tag.
          </video>

          <span
            v-else-if="config_.fileType == 'other' && files[index].url"
            class="fileName"
            >{{ files[index].url.split("/").pop() }}</span
          >

          <div class="btnDelete">❌</div>
        </template>

        <!-- Label -->
        <span v-else>{{ config_.label }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

export default {
  name: "VueEasyMultiFileUpload",
  props: {
    config: {
      type: Object,
    },

    value: {
      type: null,
    },
  },

  data() {
    return {
      config_: {
        id: "multi-file-uploader",
        label: "Upload a file",
        uploadingMessage: "Uploading...",
        maxFiles: 1,
        uploadUrl: null,
        deleteUrl: null,
        uploadHttpMethod: "POST",
        deleteHttpMethod: "DELETE",
        Authorization: null,
        width: "150px",
        height: "100px",
        allowExt: ["jpg", "png", "gif"],
        fileType: "image", // image, video, other
        maxSize: 5, // in mb,
        delimiter: null,
      },
      files: null,
      activeIndex: null,
    };
  },

  created() {
    if (this.config) {
      Object.keys(this.config_).forEach((key) => {
        if (this.config[key]) Vue.set(this.config_, key, this.config[key]);
      });
    }
    this.files = Array(this.config_.maxFiles);

    if (this.value) {
      if (typeof this.value == "string")
        this.value = this.value
          .split(this.config.delimiter)
          .filter((v) => (v && v.length ? true : false));
      this.value.forEach((v, i) =>
        Vue.set(this.files, i, { url: v, loading: false })
      );
    }
  },

  methods: {
    handleFileListItemClick(index, mode) {
      // mode => true: create, false: delete

      if (mode) {
        // ignore if file already uploaded
        if (this.files[index] && this.config.deleteUrl) return;

        this.activeIndex = index;
        this.$refs.fileInput.click();
      } else {
        if (confirm("Are you sure you want to delete?")) {
          if (!this.config.deleteUrl) {
            Vue.set(this.files, index, null);
            return;
          }

          let url = this.files[index].url;
          Vue.set(this.files, index, { loading: true, url: null });

          fetch(this.config_.deleteUrl, {
            method: this.config_.deleteHttpMethod,
            headers: {
              "Content-Type": "application/json;charset=utf-8",
              Authorization: this.config_.Authorization,
            },
            body: JSON.stringify({ filePath: url }),
          })
            .then(() => {
              Vue.set(this.files, index, null);
              this.update();
            })
            .catch((e) => {
              console.log(e);
            });
        }
      }
    },

    update() {
      let paths = this.files
        .filter((f) => (f ? true : false))
        .map((f) => f.url);
      this.$emit(
        "input",
        this.config_.delimiter ? paths.join(this.config_.delimiter) : paths
      );

      this.$refs.fileInput.value = null;
    },

    handleFileChange(e) {
      let file = e.target.files.item(0);

      // validate file
      const { name: fileName, size: fileSize } = file;
      const fileEx = fileName.split(".").pop();
      if (!this.config_.allowExt.includes(fileEx)) {
        alert("file type not allowed");
        return;
      } else if (fileSize > this.config_.maxSize * 1024 * 1024) {
        alert("file size too large");
        return;
      }

      Vue.set(this.files, this.activeIndex, { loading: true, url: null });

      // upload file
      var formData = new FormData();
      formData.append("file", file);

      fetch(this.config_.uploadUrl, {
        method: this.config_.uploadHttpMethod,
        body: formData,
        headers: {
          Authorization: this.config_.Authorization,
        },
      })
        .then((response) => response.json())
        .then((response) => {
          Vue.set(this.files, this.activeIndex, {
            url: response.filePath,
            loading: false,
          });
          this.update();
        })
        .catch((e) => {
          alert("Error uploading file");
          console.log(e);
          Vue.set(this.files, this.activeIndex, null);
        });
    },
  },

  computed: {
    fileListItemStyle() {
      return `width: ${this.config_.width}; height: ${this.config_.height};`;
    },
  },

  watch: {
    value: function (newVal, oldVal) {
      if (!newVal) return;
      if (newVal + "" !== oldVal + "") {
        if (typeof newVal == "string")
          newVal = newVal
            .split(this.config.delimiter)
            .filter((v) => (v && v.length ? true : false));
        newVal.forEach((v, i) =>
          Vue.set(this.files, i, { url: v, loading: false })
        );
      }
    },
  },
};
</script>

<style scoped>
.filesList {
  display: flex;
  flex-wrap: wrap;
}

.filesList > .filesListItem {
  border-radius: 0.25rem;
  border: 2px dashed #373737;
  background-color: whitesmoke;
  text-align: center;
  cursor: pointer;
  margin: 0.5rem;
  position: relative;
}

.filesListItem .btnDelete {
  position: absolute;
  top: 0;
  right: 0;
  width: 25px;
  height: 25px;
  line-height: 25px;
  font-size: 0.6rem;
  border-radius: 50%;
  display: flex;
  justify-content: center;

  transform: translate(50%, -50%);
  border: 2px dashed #373737;
  background-color: whitesmoke;
  opacity: 0;
  transition: all 0.15s;
}

.filesListItem:hover .btnDelete {
  opacity: 1;
}

.filesListItem img,
video,
span {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

  max-width: 90%;
  max-height: 90%;
  object-fit: cover;
}

.fileName {
  font-size: 0.8rem;
  overflow: hidden;
}
</style>

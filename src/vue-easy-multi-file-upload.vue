<template>
  <div :id="config_.id">
    <input type="file" ref="fileInput" hidden @change="handleFileChange" />

    <div class="filesList">
      <div
        class="filesListItem"
        :class="{ '--uploaded': files[index] }"
        v-for="(i, index) in config_.maxFiles || 1"
        :key="index"
        :style="config_.style"
        @click="handleFileListItemClick(index, files[index] ? false : true)"
      >
        <template v-if="files && files[index]">
          <!-- Loading -->
          <div
            class="message"
            v-if="files[index].loading"
            v-html="config_.uploadingMessage"
          ></div>

          <!-- Upload Error -->
          <div
            class="message"
            v-else-if="!files[index].loading && !files[index].url"
          >
            Error uploading !!
          </div>

          <!-- File Types Preview -->
          <template v-if="files[index].url">
            <!-- image -->
            <img v-if="files[index].type == 'image'" :src="files[index].url" />

            <!-- Video -->
            <video v-else-if="files[index].type == 'video'" loop controls :src="files[index].url">
              <source :src="files[index].url" />
              Your browser does not support the video tag.
            </video>

            <!-- Other -->
            <div class="message fileName" v-else>
              {{ files[index].url.split("/").pop() }}
            </div>
          </template>

          <!-- Btn Delete -->
          <div class="btnDelete">❌</div>
        </template>

        <!-- Label -->
        <div class="message" v-else v-html="config_.label"></div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

const determineFileTypeByExt = (ext) => {
  const TYPES_DICT = {
    web: "css less scss wasm",
    audio:
      "aac aiff ape au flac gsm it m3u m4a mid mod mp3 mpa pls ra s3m sid wav wma xm",
    code: "c cc class clj cpp cs cxx el go h java lua m m4 php pl po py rb rs swift vb vcxproj xcodeproj xml diff patch html js",
    slide: "ppt odp ",
    sheet: "ods xls xlsx csv ics vcf",
    image:
      "3dm 3ds max bmp dds gif jpg jpeg png psd xcf tga thm tif tiff ai eps ps svg dwg dxf gpx kml kmz webp",
    archiv:
      "7z a apk ar bz2 cab cpio deb dmg egg gz iso jar lha mar pea rar rpm s7z shar tar tbz2 tgz tlz war whl xpi zip zipx xz pak",
    book: "mobi epub azw1 azw3 azw4 azw6 azw cbr cbz",
    text: "doc docx ebook log md msg odt org pages pdf rtf rst tex txt wpd wps",
    exec: "exe msi bin command sh bat crx",
    font: "eot otf ttf woff woff2",
    video:
      "3g2 3gp aaf asf avchd avi drc flv m2v m4p m4v mkv mng mov mp2 mp4 mpe mpeg mpg mpv mxf nsv ogg ogv ogm qt rm rmvb roq srt svi vob webm wmv yuv",
  };
  let TYPE = "other";
  Object.keys(TYPES_DICT).forEach((type) => {
    if (TYPES_DICT[type].split(" ").includes(ext.toLowerCase())) TYPE = type;
  });
  return TYPE;
};

export default {
  name: "VueEasyMultiFileUpload",
  props: {
    config: { type: Object },
    value: { type: null },
  },
  data() {
    return {
      config_: {
        id: "multi-file-uploader",
        label: "Upload a file",
        uploadingMessage: "Loading...",
        maxFiles: null,
        uploadUrl: null,
        deleteUrl: null,
        uploadHttpMethod: "POST",
        deleteHttpMethod: "DELETE",
        uploadFieldName: "file",
        deleteFieldName: "filePath",
        Authorization: null,
        style: null,
        allowExt: ["jpg", "png", "gif", "mp4", "txt", "pdf"],
        maxSize: null,
        delimiter: null,
        customValidator: null,
      },
      files: null,
      activeIndex: null,
    };
  },
  created() {
    // Apply config
    if (this.config) {
      Object.keys(this.config_).forEach((key) => {
        if (this.config[key]) Vue.set(this.config_, key, this.config[key]);
      });
      this.processValueProp(this.value);
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
      } else this.deleteFile(index);
    },
    deleteFile(index) {
      if (confirm("Are you sure you want to delete?")) {
        if (!this.config.deleteUrl) {
          Vue.set(this.files, index, null);
          return;
        }
        let url = this.files[index].url;
        Vue.set(this.files, index, {
          ...this.files[index],
          loading: true,
          url: null,
        });
        fetch(this.config_.deleteUrl, {
          method: this.config_.deleteHttpMethod,
          headers: {
            "Content-Type": "application/json;charset=utf-8",
            Authorization: this.config_.Authorization,
          },
          body: JSON.stringify({ [this.config_.deleteFieldName]: url }),
        })
          .then(() => {
            Vue.set(this.files, index, null);
            this.update();
          })
          .catch((e) => {
            console.log(e);
          });
      }
    },
    update() {
      let paths = this.files
        // .filter((f) => (f ? true : false))
        .map((f) => (f ? f.url : null));
      let valueToEmit = this.config_.delimiter
        ? paths.join(this.config_.delimiter)
        : paths;
      this.$emit("input", valueToEmit);
      this.$refs.fileInput.value = null; // clear fileInput
    },
    async handleFileChange(e) {
      let file = e.target.files.item(0);
      // validate file
      const { name: fileName, size: fileSize } = file;
      let fileExt = fileName.split(".").pop();
      if(fileExt) fileExt = fileExt.toLowerCase();

      let error = null;
      if (!this.config_.allowExt.includes(fileExt))
        error = "file type not allowed";
      else if (fileSize > this.config_.maxSize * 1024 * 1024)
        error = "file size too large";
      else if (this.config_.customValidator) {
        try {
          await this.config_.customValidator(file);
        } catch (e) {
          error = e.message;
        }
      }

      if (error) {
        alert(error);
        return;
      }

      Vue.set(this.files, this.activeIndex, {
        loading: true,
        url: null,
        type: determineFileTypeByExt(fileExt),
      });
      // upload file
      var formData = new FormData();
      formData.append(this.config_.uploadFieldName, file);
      fetch(this.config_.uploadUrl, {
        method: this.config_.uploadHttpMethod,
        body: formData,
        headers: { Authorization: this.config_.Authorization },
      })
        .then((response) => response.json())
        .then((response) => {
          // success
          Vue.set(this.files, this.activeIndex, {
            ...this.files[this.activeIndex],
            url: response.filePath,
            loading: false,
          });
          this.update();
        })
        .catch(() => {
          // fail
          alert("Error uploading file");
          Vue.set(this.files, this.activeIndex, {
            ...this.files[this.activeIndex],
            loading: false,
          });
          this.update();
        });
    },
    processValueProp(val) {
      this.files = Array(this.config_.maxFiles);
      if (!val) return;

      if (typeof val == "string") val = val.split(this.config_.delimiter);
      val.forEach((v, i) => {
        if (v)
          Vue.set(this.files, i, {
            url: v,
            loading: false,
            type: v ? determineFileTypeByExt(v.split(".").pop()) : "other",
          });
      });
    },
  },
  watch: {
    value: function (newVal, oldVal) {
      if (newVal + "" !== oldVal + "") {
        this.processValueProp(newVal);
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
  border: 1.5px dashed lightgray;
  background-color: whitesmoke;
  text-align: center;
  cursor: pointer;
  margin: 0.5rem;
  position: relative;
  font-size: 0.8rem;
  font-family: Arial, Helvetica, sans-serif;
  color: grey;
}
.--uploaded {
  border-color: #373737 !important;
  color: #373737 !important;
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
  border: 1.5px dashed #373737;
  background-color: whitesmoke;
  opacity: 0;
  transition: all 0.15s;
}
.filesListItem:hover .btnDelete {
  opacity: 1;
}
.filesListItem img,
video,
div.message {
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
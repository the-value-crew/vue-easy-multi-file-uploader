# vue-easy-multi-file-uploader

File uploading made easy for vueJs. The package is currently available for Vue 2 only.

## How to use

### 1. Install package via npm
```bash 
npm i vue-easy-multi-file-upload --save
```

### 2. Import & use

```vue
<template>
  <div id="app">
    <VueEasyMultiFileUpload :config="config" v-model="values" />
    {{values}}
  </div>
</template>

<script>
import VueEasyMultiFileUpload from "vue-easy-multi-file-upload";

export default {
  components: { VueEasyMultiFileUpload },
  data() {
    return {
      config: {
        id: "easy-multi-file-uploader",
        maxFiles: 3,
        uploadUrl: "http://your-website/file-upload",
        Authorization: "Bearer ...",
        width: "90px",
        height: "75px",
        allowExt: ["jpg", "png", "gif"],
        fileType: "photo",
        maxSize: 5,
      },
      values: null
    };
  },
};
</script>
```

# props

There is only one prop i.e. ***config***. Available configurationa are mentioned below.

| Name             | Description                                                                                  | Required |
|------------------|----------------------------------------------------------------------------------------------|----------|
| id               | id of component                                                                              | true     |
| label            | This text is shown at initial state i.e. when no files are selected. Default: `Uploading...` |          |
| uploadingMessage | Message to show when file is being uploaded.                                                 |          |
| maxFiles         | Max no. of files                                                                             |          |
| uploadUrl        | URL to send request to                                                                       | true     |
| deleteUrl        | URL to delete uploaded file                                                                  |          |
| uploadHttpMethod | HTTP method for file upload.                                                                 | true     |
| deleteHttpMethod | HTTP method for file delete.                                                                 |          |
| Authorization    | Authorization header                                                                         |          |
| width            | `width` of file upload UI in px                                                              |          |
| height           | `height` of file upload UI in px                                                             |          |
| allowExt         | File extensions to allow for upload.                                                         | true     |
| fileType         | File Type. Options: `image`, `video`, 'other'                                                | true     |
| maxSize          | Max size of file. In MB                                                                      |          |
| delimiter        | Delimiter combining file urls                                                                |          |

# events

| Name             | Description                                                                                  | Required |
|------------------|----------------------------------------------------------------------------------------------|----------|
| @input           | Fired when new file is uploaded or existing file is deleted                                  |          |
# vue-easy-multi-file-uploader

File uploading made easy for vueJs. Custom validators, custom styling, file preview support for image & videos...

> Please use the latest version.

![Snapshot!](https://raw.githubusercontent.com/the-value-crew/vue-easy-multi-file-uploader/master/docs/assets/img/snapshot.fd19a490.png)

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
        id: "multi-file-uploader",
        label: "Upload your files",
        maxFiles: 6,
        uploadUrl: "...",
        deleteUrl: "...",
        uploadHttpMethod: "POST",
        deleteHttpMethod: "DELETE",
        uploadFieldName: "file",
        deleteFieldName: "filePath",
        Authorization: "Bearer ...",
        style: {
          width: "90px",
          height: "75px",
        },
        allowExt: ["jpg", "png", "gif", "mp4", "txt", "webm", "pdf"],
        maxSize: 5,
        delimiter: "|",
        customValidator: (file) => {
          // ... custom validation logic ...
          return {
            status: true, // or false
            message: "It passes validation" // or null
          }
          // Tip: function can be async, can also return promise
        }
      },
      values: null
    };
  },
};
</script>
```

## Props

There is only one prop i.e. ***config***. Fields marked with ***asterisk(\*)*** are required.

| Name               | Description                                                                                                                                                                                                                                                                                 | Default Value                                    |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| ****uploadUrl\****        | URL to upload file. Specified request is sent as [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData).                                                                                                    |                                                  |
| ***uploadHttpMethod\**** | HTTP method for ***uploadUrl***. Generally it's either POST or PUT.                                                                                                                                                                                                                    | `POST`                                         |
| ***uploadFieldName\****  | Name of key for FormData that holds file.                                                                                                                                                                                                                                                   | `file`                                         |
| deleteUrl          | URL to delete file. Specified request is sent to this url with `application/json` ContentType & uploaded file's path.                                                                                                                                                                     |                                                  |
| deleteHttpMethod   | HTTP method for ***deleteUrl***. Generally its DELETE, can also be POST or PUT.                                                                                                                                                                                                       | `DELETE`                                       |
| deleteFieldName    | fieldName containing file path to delete.              | `filePath`                                                                                                                                                                                                                                     |                                                  |
| Authorization      | Authorization header. eg: Beaker token                                                                                                                                                                                                                                                      |                                                  |
| ***allowExt\****         | File extensions to allow. Used for validating file before upload.                                                                                                                                                                                                                           | `["jpg", "png", "gif", "mp4", "txt", "pdf"]` |
| maxSize            | Max size of file in Mb.                                                                                                                                                                                                                                                                     |                                                  |
| maxFiles           | Maximum number of files to upload                                                                                                                                                                                                                                                           |                                                  |
| label              | Text to show at initial state. ***Also supports HTML.***                                                                                                                                                                                                                                          | Upload a file                                    |
| uploadingMessage   | Text to show at uploading state. ***Also supports HTML.***                                                                                                                                                                                                                                         | Loading...                                     |
| style              | Custom style for file upload section. Accepts style in plain [string or object](https://vuejs.org/v2/guide/class-and-style.html#Object-Syntax-1) |                                                  |
| delimiter          | If delimiter is specified, a single string containing file paths seperated by delimeter is returned. eg: pipes, comma                                                                                                                                                                       |                                                  |
| id                 | Id of parent element                                                                                                                                                                                                                                                                        | `multi-file-uploader`                          |
| customValidator | Custom validation for uploaded file before sending it to server. View example above. | 
| compress | Compress images. Uses compressorjs | false |
|compressorOptions | Compressorjs options. View [compressorjs](https://github.com/fengyuanchen/compressorjs) for more details | null
## Events

| Name   | Description                                                 | 
| ------ | ----------------------------------------------------------- | 
| @input | Fired when new file is uploaded or existing file is deleted |
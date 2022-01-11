<template>
  <div id="webviewer" ref="viewer" style="display: none"></div>
  <!-- <div id="webviewer" ref="viewer"></div> -->
  <div>sup {{previewHeight}}</div>
  <input type="file" @change="handleFileChange"/>
  <canvas
    id="previewCanvas"
    :width="previewWidth"
    :height="previewHeight"
  ></canvas>
</template>

<script>
// import { ref, onMounted } from "vue";
import WebViewer from "@pdftron/webviewer";

export default {
  name: "WebViewer",
  props: { initialDoc: { type: String } },
  data() {
    return {
      webviewer: null,
      previewWidth: 100,
      previewHeight: 100
    }
  },
  async mounted() {
    let path = `${process.env.BASE_URL}webviewer`;
    this.webviewer = await WebViewer({ path, initialDoc: "https://pdftron.s3.amazonaws.com/downloads/pl/demo-annotated.pdf"}, document.getElementById('webviewer'));
  },
  methods: {
    handleFileChange(event) {
      console.log('viewer ', this.webviewer);
      Array.from(event.target.files).map((javascriptFile) => {
        this.webviewer.CoreControls.createDocument(javascriptFile).then((webviewerDoc) => {
          webviewerDoc.loadCanvasAsync({
            pageNumber: 1,
            width: this.previewWidth,
            height: this.previewHeight,
            drawComplete: (loadedCanvas) => {
              let canvasContext = document.getElementById('previewCanvas').getContext('2d');
              canvasContext.clearRect(0, 0, this.previewWidth, this.previewHeight);
              canvasContext.drawImage(loadedCanvas, 0, 0);
            },
          })
        });
      })
    }
  }
};
</script>

<style>
#webviewer {
  height: 100vh;
}
</style>

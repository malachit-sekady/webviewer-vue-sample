<template>
  <div id="webviewer" ref="viewer" style="display: none"></div>
  <div>sup {{previewHeight}}</div>
  <input type="file" @change="handleFileChange"/>
  <div>
  <canvas
    id="previewCanvas"
    :width="previewWidth"
    :height="previewHeight"
  ></canvas>
  </div>
</template>

<script>
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
    let webviewerScript = document.createElement('script');
    webviewerScript.setAttribute('src', 'webviewer/core/webviewer-core.min.js');
    document.head.appendChild(webviewerScript);
    let path = `${process.env.BASE_URL}webviewer`;
    this.webviewer = await WebViewer({ path, initialDoc: "https://pdftron.s3.amazonaws.com/downloads/pl/demo-annotated.pdf"}, document.getElementById('webviewer'));
  },
  methods: {
    handleFileChange(event) {
      console.log('viewer ', this.webviewer);
      console.log('core controls ', window.CoreControls);
      window.Core.setWorkerPath('webviewer/core');
      Array.from(event.target.files).map((javascriptFile) => {
        // this.webviewer.CoreControls.createDocument(javascriptFile).then((webviewerDoc) => {
        window.Core.createDocument(javascriptFile).then((webviewerDoc) => {
        // window.CoreControls.createDocument(javascriptFile).then((webviewerDoc) => {
          webviewerDoc.loadCanvasAsync({
            pageNumber: 1,
            width: this.previewWidth,
            height: this.previewHeight,
            multiplier: 1,
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

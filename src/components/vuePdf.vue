<template>
  <div>
              <!-- <previewPdf
                ref="pdf"
                :src="pdfUrl"
                :page="currentPage"
                @num-pages="pageCount = $event"
                @progress="loadedRatio = $event"
                @page-loaded="currentPage = $event"
                @loaded="loadPdfHandler"
              ></previewPdf> -->
  </div>
</template>

<script>
import previewPdf from "vue-pdf";
  export default {
  //   props: ["pdfUrl"],
  //   methods: {
  //         // pdf加载时
  //   loadPdfHandler() {
  //     this.currentPage = 1; // 加载的时候先加载第一页
  //   },
  //   // 初始化pdf
  //   async loadPDFFileData() {
  //     const url = this.pdfUrl;
  //     console.log(url)
  //     this.pdfUrl = previewPdf.createLoadingTask({
  //       url: url,
  //       cMapUrl: "https://unpkg.com/pdfjs-dist@2.0.943/cmaps/",
  //       cMapPacked: true,
  //     });
  //   },
  //   }
  // }
  }
</script>

<style lang="scss" scoped>

</style>

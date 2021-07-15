<template>
  <div class="pdfDemo">
    <v-pdf :pdfUrl="pdfUrl"></v-pdf>
    <!-- <vuePdf :pdfUrl="pdfUrl"></vuePdf> -->
  </div>
</template>
<script>
import pdf from "./pdf";
// import vuePdf from "./vuePdf";

export default {
  data() {
    return {
      pdfUrl: "../../static/字节前端第一期面试题.pdf" //本地调试
    };
  },
  components: {
    "v-pdf": pdf,
    // "vuePdf": vuePdf
  }
};
</script>
<style>
.pdfDemo {
  border: 2px solid aqua;
}
</style>

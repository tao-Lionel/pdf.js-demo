<template>
<div class="main-container">
      <div class="find-bar">
        <input v-model="input" placeholder="请输入要搜索的内容" class="find-input" @input="handlerSearcher()" />
        <span class="span" @click="findTextPrevious()">
          上一个
        </span>
        <span class="span" @click="findTextNext()">
          下一个
        </span>
      </div>
  <button class="" @click="changePagePrevious()">上一页</button>
  <button class="" @click="changePageNext()">下一页</button>
  <span class="span">{{currentPageNumber}}/{{numPages}}</span>
  <span class="span">第 {{matchesCount.current}} 项, 共匹配 {{matchesCount.total}}项</span>
        <div id="viewerContainer" ref="containerRef" class="viewerContainer">
          <div id="viewer" class="pdfViewer"></div>
        </div>
</div>

</template>
<script>
import pdfjs from "pdfjs-dist";
import {
  PDFLinkService,
  PDFViewer,
  PDFFindController
} from "pdfjs-dist/web/pdf_viewer";
import "pdfjs-dist/web/pdf_viewer.css";
import pdfjsWorker from "pdfjs-dist/build/pdf.worker";
pdfjs.GlobalWorkerOptions.workerSrc = pdfjsWorker;

export default {
  name: "pdf",
  data() {
    return {
      pages: 0,
      currentPageNumber: 1,
      numPages: 0,
      newViewer: {},
      matchesCount: {},
      input: "",
      searcher: {
        phraseSearch: true, // 是否短语查找
        query: "", // 查询字段
        findPrevious: undefined, // 是否循环查找
        highlightAll: true, // 是否高亮
        // caseSensitive: false, // 区分大小写
        // entireWord: false, // 整个单词
      },
      interval: {}
    };
  },
  props: ["pdfUrl"],
  methods: {
    changePageNext() {
      this.changePage(this.currentPageNumber + 1);
    },
    changePagePrevious() {
      this.changePage(this.currentPageNumber - 1);
    },
    changePage(num) {
      this.newViewer.currentPageNumber = num; // 改变页码
      this.currentPageNumber = num;
    },
    handlerSearcher() {
      let query = this.input.trim();
      this.searcher.query = query;
      this.newViewer.findController.executeCommand("find", this.searcher);
    },
    findTextPrevious() {
      this.searcher.findPrevious = true;
      this.newViewer.findController.executeCommand("findagain", this.searcher);
    },
    findTextNext() {
      this.searcher.findPrevious = false;
      this.newViewer.findController.executeCommand("findagain", this.searcher);
    },
    /*加载PDF文件*/
    loadPdf(url) {
      const linkService = new PDFLinkService();
      const findController = new PDFFindController({
        linkService,
      });
      let _this = this;
      // PDFSinglePageViewer 单页控制器
      // PDFViewer 多页控制器
      const newViewer = new PDFViewer({
        container: _this.$refs.containerRef, // 显示PDF的容器dom
        linkService,
        useOnlyCssZoom: true, // 否可以通过css控制页面的缩放,默认 false
        textLayerMode: 1, // 显示文字类型 渲染文字图层
        // renderer:'svg', // 用什么方式渲染 可选canvas和svg
        findController, // 文字查找控制器

      });
      linkService.setViewer(newViewer);
      // 缩放
      newViewer.currentScaleValue = "page-width"; // 无效 暂且不知道为什么
      pdfjs.getDocument(url).promise.then((pdf) => {
        if (pdf) {
          // 总页数
          _this.numPages = pdf.numPages;
          newViewer.setDocument(pdf);
          linkService.setDocument(pdf);
          // 全局的newViewer
          _this.newViewer = newViewer;
          // 判断是否已经渲染完毕
          _this.interval = setInterval(() => {
            _this.loadCompletePdf();
          }, 100);
        }
      });
    },
    loadCompletePdf() {
      if (this.newViewer.pageViewsReady) {
        clearInterval(this.interval);
      }
    },
  },
  mounted: function() {
    this.loadPdf(this.pdfUrl);
    const that = this;
    document.addEventListener("pagechanging", function(evt) {
      const page = evt.detail.pageNumber;
      that.changePage(page);
    });
    window.addEventListener("updatefindmatchescount", e => {
      that.matchesCount = e.detail.matchesCount;
    });
    window.addEventListener("updatefindcontrolstate", e => {
      that.matchesCount = e.detail.matchesCount;
    });
  }
};
</script>
<style>
.span {
  font-size: 16px;
}
.pagination {
  height: 50px;
}
.find-bar {
  height: 50px;
      display: flex;
    justify-content: center;
    align-items: center;
}
/* .main-container {
  position: relative;
} */
.viewerContainer {
  overflow: auto;
  position: absolute;
  top: 350px;
  right: 0;
  bottom: 0;
  left: 0;
  outline: none;
}
</style>

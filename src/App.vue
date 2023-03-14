<template>
  <div id="app">
    <ShowMenu v-on:listenShowMenu="listenShowMenu"></ShowMenu>
    <ShowStyle ref="comShowStyle"></ShowStyle>
    <ShowResume ref="comShowResume" :resumeData="resumeData"></ShowResume>
    <transition name="fade">
      <FormList
        ref="comFormList"
        :resumeData="resumeData"
        v-if="formListShow"
        v-on:listenFormList="listenFormList"
      ></FormList>
    </transition>
  </div>
</template>

<script>
import ShowMenu from "./components/menu/ShowMenu.vue";
import ShowStyle from "./components/showStyle/ShowStyle.vue";
import ShowResume from "./components/showResume/ShowResume.vue";
import FormList from "./components/form/FormList.vue";
import resumeData from "../static/resumedata.json";
import { str } from "./config/comstr.js";

export default {
  name: "app",
  data() {
    return {
      fromDataT: {},
      formListFlag: false,
      formListShow: false,
      resumeData: resumeData,
      code: str.code,
    };
  },
  created() {
    let n = 0;
    let _this = this;

    this.$nextTick(function () {
      let len = _this.code.length;
      // 每10ms 写入一次
      var setIn = setInterval(function () {
        // 只显示作用
        _this.$refs.comShowStyle.writeStyleCode(_this.code.substring(0, n));
        // 渲染作用
        _this.$refs.comShowResume.responseStyleCode(_this.code.substring(0, n));
        n++;
        if (n >= len) {
          // 停止
          clearInterval(setIn);
        }
      }, 10);
    });
  },
  methods: {
    listenShowMenu: function (msg) {
      // 生成简历 事件
      if (msg.type == "fileClick") {
        this.formListShow = msg.showFlag;
        if (this.formListFlag) {
          this.resumeData.formFlag = true;
        }
      }
      // 下载简历 事件
      if (msg.type == "choiceClick") {
        var resumeName =
          this.resumeData.head.intention +
          "-" +
          this.resumeData.head.name +
          "-" +
          this.resumeData.head.tel;
        var htmlcode = document.getElementById("show-resume");
        htmlcode.style.width = msg.size.width + "px";
        htmlcode.style.height = msg.size.height + "px";
        // html 转 canvas 再转 pdf
        html2canvas(htmlcode, {
          scale: window.devicePixelRatio, // 提高清晰度
          onrendered: function (canvas) {
            var imgData = canvas.toDataURL("image/png");
            //Default export is a4 paper
            var doc = new jsPDF();
            doc.addImage(imgData, "PNG", 10, 10);
            doc.save(resumeName + ".pdf");
          },
        });
      }
    },
    listenFormList: function (msg) {
      if (msg.type == "createResClick") {
        this.formListShow = msg.showFlag;
      }
      if (msg.type == "fromData") {
        this.resumeData = msg.fromData;
        this.formListFlag = true;
      }
    },
  },
  components: {
    ShowMenu,
    ShowStyle,
    ShowResume,
    FormList,
  },
};
</script>

<style>
/* 内外边距通常让各个浏览器样式的表现位置不同 */
body,
div,
dl,
dt,
dd,
ul,
ol,
li,
h1,
h2,
h3,
h4,
h5,
h6,
pre,
code,
form,
fieldset,
legend,
input,
textarea,
p,
blockquote,
th,
td,
hr,
button,
article,
aside,
details,
figcaption,
figure,
footer,
header,
menu,
nav,
section {
  margin: 0;
  padding: 0;
}

input,
select,
textarea {
  font-size: 100%;
}

input {
  outline: none;
  border-style: none;
}

/* 去掉各 Table  cell 的边距并让其边重合 */
table {
  border-collapse: collapse;
  border-spacing: 0;
}

/* 去除默认边框 */
fieldset,
img {
  border: 0;
}

/* 去掉 firefox 下此元素的边框 */
abbr,
acronym {
  border: 0;
  font-variant: normal;
}

/* 一致的 del 样式 */
del {
  text-decoration: line-through;
}

address,
caption,
cite,
code,
dfn,
em,
th,
var {
  font-style: normal;
  font-weight: 500;
}

/* 去掉列表前的标识, li 会继承 */
ol,
ul {
  list-style: none;
}

/* 对齐是排版最重要的因素, 别让什么都居中 */
caption,
th {
  text-align: left;
}

a {
  text-decoration: none;
}

/*清除浮动代码*/
.clearfloat:after {
  display: block;
  clear: both;
  content: "";
  visibility: hidden;
  height: 0;
}

.clearfloat {
  zoom: 1;
}

body,
html {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
  min-width: 1024px;
  font-size: 62.5%;
}

#app {
  width: 100%;
  height: 100%;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-active {
  opacity: 0;
}
</style>

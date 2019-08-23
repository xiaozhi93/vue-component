<template>
  <div class="editor">
    <div
      class="editor-core"
      contenteditable="true"
      ref="editor"
      @keyup="getCursor"
      @click="getCursor"
      @keydown.shift.enter.exact.prevent="breakLine"
      @keydown.enter.exact.prevent="submit"
      @paste.prevent="onPaste"
    ></div>
  </div>
</template>
<script>
import { getCursorPosition, onPaste } from "@/utils/cursor";
export default {
  name: "editor",
  data() {
    return {
      editor: null,
      cursorPosition: 0
    };
  },
  mounted() {
    this.editor = this.$refs["editor"];
  },
  methods: {
    getCursor() {
      this.cursorPosition = getCursorPosition(this.editor);
      console.log(this.cursorPosition);
    },
    breakLine(e) {
      console.log(e);
    },
    async onPaste(e) {
      console.log(e)
      const result = await onPaste(e);
      const imgRegx = /^data:image\/png;base64,/;
      if (imgRegx.test(result)) {
        // const sel = window.getSelection()
        // if (sel && sel.rangeCount === 1 && sel.isCollapsed) {
        //   const range = sel.getRangeAt(0)
        //   const img = new Image()
        //   img.src = result
        //   range.insertNode(img)
        //   range.collapse(false)
        //   sel.removeAllRanges()
        //   sel.addRange(range)
        // }

        document.execCommand("insertImage", false, result);
      } else {
        document.execCommand("insertText", false, result);
      }
    },
    submit(e) {
      const value = e.target.innerHTML.replace(/[\n\r]$/, "");
      if (value) {
        console.info("Submit text", { value });
        this.$emit("on-submit", value);
        e.target.innerText = "";
      }
    },
    async onPaste(e) {
      console.log(e);
    },
    insertEmoji(emoji) {
      const text = this.editor.innerHTML;
      this.editor.innerHTML =
        text.slice(0, this.cursorPosition) +
        emoji +
        text.slice(this.cursorPosition, text.length);
      setCursorPosition(this.editor, this.cursorPosition + 1);
      this.cursorPosition = getCursorPosition(this.editor) + 1; //  emoji takes 2 bytes
    }
  }
};
</script>
<style scoped>
.editor-core {
  width: 500px;
  height: 180px;
  border-radius: 4px;
  background: #fff;
  border: 1px solid #ccc;
  box-sizing: border-box;
  word-break: break-all;
  overflow-wrap: break-word;
  padding: 5px;
  outline: none;
  text-align: left;
}
</style>
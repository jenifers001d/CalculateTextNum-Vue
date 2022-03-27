<template>
  <div class="textarea-outer">
    <textarea ref="textarea" class="my-SMS" name="mySMS" rows="10" v-model="localMsg"
    @input="highlight ? highlightText : ''"
    @scroll="scrollHighlight"></textarea>
    <div ref="highlight" class="text-background" ></div>
    <div>
      <div v-if="showCharNum"> Character number: {{ charNum }}</div>
      <div v-if="showSMSNum"> SMS number: {{ SMSNum }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalculateNumTextArea',
  props: {
    msg: { type: String, default: '' },
    charsPerSMS: { type: Number, default: 10 },
    showCharNum: { type: Boolean, default: false },
    showSMSNum: { type: Boolean, default: false },
    highlight: { type: Boolean, default: true }
  },
  data () {
    return {
      localMsg: this.msg.trim(),
      localCharsPerSMS: this.charsPerSMS,
      charNum: 0,
      SMSNum: 0
    }
  },
  watch: {
    localMsg: function (value, preValue) {
      if (value !== preValue) {
        this.updateNum()
        if (this.highlight) {
          this.highlightText()
        }
      }
    },
    charsPerSMS: function (value, preValue) {
      console.log('charsPerSMS', value, preValue)
      if (value < 10) {
        this.localCharsPerSMS = 10
        this.$emit('set-minimum', this.localCharsPerSMS)
      }
      if (value !== preValue) {
        this.localCharsPerSMS = value
      }
    },
    localCharsPerSMS: function (value, preValue) {
      if (value !== preValue) {
        this.updateNum()
        this.highlightText()
        this.$refs.highlight.scrollTop = this.$refs.textarea.scrollTop
      }
    },
    highlight: function (value) {
      if (value) {
        this.highlightText()
        this.$refs.highlight.scrollTop = this.$refs.textarea.scrollTop
      } else {
        this.$refs.highlight.innerHTML = ''
      }
    }
  },
  methods: {
    updateNum () {
      if (this.localMsg.length === '') {
        this.charNum = 0
        this.SMSNum = 0
      } else {
        this.charNum = this.localMsg.length
        this.SMSNum = Math.ceil(this.localMsg.length / this.localCharsPerSMS)
      }
    },
    highlightText () {
      // Replace \n and the end of the string with \n\n
      // Make sure there is not just one white space within last <span>
      // if input "Enter" many times, which affects aligning front text and behind text
      const chars = this.localMsg.replace(/\n$/g, '\n\n')
      let slicedText, el
      this.$refs.highlight.innerHTML = ''
      for (let i = 1; i <= this.SMSNum; i++) {
        slicedText = i === this.SMSNum
          ? chars.slice(this.localCharsPerSMS * (i - 1))
          : chars.slice(this.localCharsPerSMS * (i - 1), (this.localCharsPerSMS * i))
        el = document.createElement('span')
        el.textContent = slicedText
        if (i % 2 === 0) {
          el.classList.add('highlighted-chars')
        }
        this.$refs.highlight.append(el)
      }
    },
    scrollHighlight (e) {
      this.$refs.highlight.scrollTop = e.target.scrollTop
    },
    updateLayout () {
      // Get styles
      const textareaStyles = window.getComputedStyle(this.$refs.textarea)
      this.$refs.highlight.style.zIndex = textareaStyles.getPropertyValue('z-index') - 1
      // textareaCSS.getPropertyValue("padding") doesn't work in firefox
      this.$refs.highlight.style.paddingLeft = textareaStyles.getPropertyValue('padding-left')
      this.$refs.highlight.style.paddingTop = textareaStyles.getPropertyValue('padding-top')
      this.$refs.highlight.style.paddingRight = textareaStyles.getPropertyValue('padding-right')
      this.$refs.highlight.style.paddingBottom = textareaStyles.getPropertyValue('padding-bottom')
      this.$refs.highlight.style.width = textareaStyles.getPropertyValue('width')
      this.$refs.highlight.style.height = textareaStyles.getPropertyValue('height')
    }
  },
  mounted () {
    this.updateLayout()
    if (this.localMsg !== '') {
      this.updateNum()
      if (this.highlight) {
        this.highlightText()
      }
    }
  }
}
</script>

<style>
.textarea-outer{
  box-sizing: border-box;
  position: relative;
  width: 820px;
  margin: auto;
}
.my-SMS{
  box-sizing: border-box;
  border: 1px solid rgb(118, 118, 118);
  z-index: 2;
  position: relative;
  width: 100%;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 16px;
  background-color: transparent;
  overflow: auto;
}
.text-background{
  box-sizing: border-box;
  border: 1px solid transparent;
  position: absolute;
  top: 0px;
  right: 0;
  color: transparent;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 16px;
  text-align: left;
  overflow: auto;
  scrollbar-width: none;
  white-space: pre-wrap;
}
.text-background::-webkit-scrollbar{
  display: none;
}
.highlighted-chars {
  background-color: #f9e9d6;
}
</style>

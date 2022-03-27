<template>
  <div id="app">
    <h1>Textarea with number counter and highlight</h1>
    <div>Please check/uncheck options to toggle features.</div>
    <div style="display: inline-block; margin-right: 10px;">
      <input type="checkbox" name="character" id="character" v-model="showCharNum">
      <label for="character">Show Character Number</label>
    </div>
    <div style="display: inline-block; margin-right: 10px;">
      <input type="checkbox" name="sms" id="sms" v-model="showSMSNum">
      <label for="sms">Show SMS Number</label>
    </div>
    <div style="display: inline-block;">
      <input type="checkbox" name="highlight" id="highlight" v-model="highlight">
      <label for="highlight">Toggle highlighted text background</label>
    </div>
    <br/>
    <br/>
    <div v-if="highlight">
      <label for="charsNum" style="margin-right: 10px;">The number of characters per SMS:</label>
      <input type="input" name="charsNum" id="charsNum" v-model.number="charsNum" @keyup.enter="enter">
      <button type="button" @click="enter" style="margin-left: 10px;">Enter</button>
    </div>
    <br/>
    <CalculateNumTextArea :msg="msg" :showCharNum="showCharNum" :showSMSNum="showSMSNum"
    :highlight="highlight" :charsPerSMS="charsPerSMS" v-on:set-minimum="setMinimum"/>
  </div>
</template>

<script>
import CalculateNumTextArea from './components/CalculateTextNum.vue'

export default {
  name: 'App',
  components: {
    CalculateNumTextArea
  },
  data () {
    return {
      msg: 'Please input your message!',
      charsNum: 20,
      charsPerSMS: 20,
      showCharNum: true,
      showSMSNum: true,
      highlight: true
    }
  },
  methods: {
    enter: function () {
      this.charsPerSMS = this.charsNum
    },
    setMinimum: function (min) {
      this.charsNum = min
      this.charsPerSMS = min
      alert('The minimum of numbers should be 10.')
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

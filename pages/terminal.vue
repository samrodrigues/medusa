<template>
  <main>
    <div class="message welcome">
      CERBERUS v2422.1
    </div>
    <div class="message" v-for="message in messages" :class="message.className">
      {{ message.content }}
    </div>
    <span class="caret-block">&gt;</span>
    <input ref="inputField" class="caret-block" :class="{'submitted blink': isSubmitting}" type="text" v-model="newMessage" @keyup.enter="postMessage">
    <div class="cursor" v-if="!isSubmitting"></div>
    <div class="loader message" v-if="isSubmitting">
      <span class="loader__dot">.</span><span class="loader__dot">.</span><span class="loader__dot">.</span>
    </div>
  </main>
</template>
<script>
import axios from 'axios';

export default {
  name: "Terminal",
  props: {},
  data() {
    return {
      implementation: "",
      sessionId: "",
      newMessage: "",
      isSubmitting: false,
      messages: [],
    }
  },
  mounted() {
    this.implementation = this.$route.query.implementation;
    this.sessionId = this.$route.query.session_id || 'test';
    setTimeout(() => {
      this.$refs.inputField.focus();
    },200);
  },
  methods: {
    async postMessage () {
      this.isSubmitting = true;
      const {data} = await axios.post(`https://cerberus-vsm8.onrender.com/api/implementations/${this.implementation}`, {message: this.newMessage, session_id: this.sessionId})
      // const {data} = await axios.post(`http://localhost:8000/api/implementations/${this.implementation}`, {message: this.newMessage, session_id: this.sessionId})
      this.isSubmitting = false;
      this.messages.push({"content": this.newMessage, className: 'user'})
      this.messages.push({"content": data.response || 'bad response', className: 'bot'})
      this.newMessage = '';
    }
  }
}
</script>
<style scoped lang="scss">
main {
  background-color: #282c34;
  color: #ededed;
  font-family: monospace;
  padding: 24px 24px 48px;
}
input {
  background-color: transparent;
  padding-left: 6px;
  color: #ededed;
  border: none;
  outline: none;
  caret-color: #dedede;
  position: relative;
  caret-shape: block;
  font-family: monospace;
  margin-bottom: 6px;
  width: calc(100% - 48px);
}
input.submitted {
  color: #878787;
  caret-color: transparent;
}
input:focus + .cursor,
input:active + .cursor {
  opacity: 0;
}
.cursor {
  opacity: 1;
  position: relative;
  left: 0;
  margin-top: -5px;
  margin-left: 14px;
  height: 1.5px;
  width: 11px;
  background-color: #dedede;
  animation: 1s cursor infinite;
}

pre {
  padding: 0;
}
.caret-block > span {
  background-color: hsla(1, 1%, 100%, 1);
  color: #000;
}

@keyframes cursor {50% { opacity: 0 }}
@keyframes blink {50% { color: transparent }}
.loader__dot { animation: 1s blink infinite }
.loader__dot:nth-child(2) { animation-delay: 250ms }
.loader__dot:nth-child(3) { animation-delay: 500ms }


input:focus, input:active {
  outline: none;
}
.message {
  margin-bottom: 6px;
}
.message:before {
  content: '> ';
}
.message.welcome:before {
  content: '#! ';
}
.user {
  color: #878787;
}
</style>
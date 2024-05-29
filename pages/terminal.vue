<template>
  <main>
    <div v-for="message in messages">
      {{ message.content }}
    </div>
    <input type="text" v-model="newMessage" @keyup.enter="postMessage">
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
      newMessage: "",
      messages: [],
    }
  },
  mounted() {
    this.implementation = this.$route.query.implementation;
  },
  methods: {
    async postMessage () {
      const {data} = await axios.post(`https://cerberus-vsm8.onrender.com/api/implementations/${this.implementation}`, {message: this.newMessage})
      this.messages.push({"content": this.newMessage})
      this.messages.push({"content": data?.response?.[0]?.[1] || 'bad response'})
      this.newMessage = '';
    }
  }
}
</script>
<style scoped>

</style>
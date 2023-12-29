<script setup>

</script>

<template>
  <div ref="messagesContainer" class="messages-container">
    <div v-for="(message, index) in messages" :key="index" :class="{ 'sent-message': message.sent, 'received-message': !message.sent }" class="message">
      <div v-html="renderMarkdown(message.content)" class="markdown-content"></div>
    </div>
  </div>
</template>

<script>

import MarkdownIt from 'markdown-it';
import hljs from 'highlight.js';
import 'highlight.js/styles/github.css';
import ClipboardJS from 'clipboard';
import '@fortawesome/fontawesome-free/css/all.css';

const md = new MarkdownIt({
  highlight: function (str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return '<div class="code-container"><pre class="hljs" style="background-color: #2d2d2d; color: #fff" ><icon class="copy-button" style="margin: 40px" data-clipboard-target=".code-container"><i class="fas fa-copy"></i></icon>\n\n <code ">' +
            hljs.highlight(lang, str, true).value +
            `</code></pre></div>`;
      } catch (__) {
        console.log(__);
      }
    }

    return '<pre class="hljs" style="background-color: #2d2d2d; color: #fff"><code>' + md.utils.escapeHtml(str) + '</code></pre>';
  }
});
export default {
  props: {
    messages: Array
  },
  watch: {
    messages() {
      this.scrollMessagesToBottom();
    }
  },
  mounted() {
    this.scrollMessagesToBottom();
    new ClipboardJS('.copy-button', {
      text: function (trigger) {
        return trigger.previousElementSibling.innerText;
      }
    });
  },
  methods: {
    renderMarkdown(content) {
      return md.render(content);
    },
    scrollMessagesToBottom() {
      const container = this.$refs.messagesContainer;
      container.scrollTop = container.scrollHeight;
    }
  },

};
</script>

<style scoped>
.messages-container {
  max-height: 100%;
  overflow-y: auto;
  border-radius: 20px;
  border: 1px solid #ccc;
  padding: 10px;
  background-color: #333; /* Fondo oscuro */
  color: #fff; /* Texto claro */
  bottom: 75px;
}
.sent-message {
  text-align: right;
  color: #00bcd4; /* Color para mensajes enviados */
}
.received-message {
  text-align: left;
  color: #ff9800; /* Color para mensajes recibidos */
}
.message {
  margin-bottom: 10px;
}
/* global.css */

/* Estilos personalizados para los bloques de código */
.hljs2  {
  background-color: #2d2d2d !important; /* Cambia esto al color de fondo deseado */
  color: #fff !important; /* Cambia esto al color de texto deseado */
}
.copy-button {
  position: absolute;
  top: 5px;
  right: 5px;
  background-color: #333;
  color: #fff;
  border: none;
  padding: 5px;
  cursor: pointer;
}
</style>
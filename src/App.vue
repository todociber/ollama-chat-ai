<template>
  <div id="app" ref="app">
    <ChatMessages :messages="messages" />
    <ChatInput @new-message="addMessage" />
  </div>
</template>

<script>
import axios from 'axios';

import ChatInput from './components/ChatInput.vue';
import ChatMessages from './components/ChatMessages.vue';

export default {
  data() {
    return {
      messages: [],
    };
  },
  methods: {
    async addMessage(message) {
      if (this.messages.length === 0 && process.env.VUE_APP_FIRST_MESSAGE !== undefined){
        this.messages.push({ content: process.env.VUE_APP_FIRST_MESSAGE , role: 'system', sent: false });
      }

      this.messages.push({ content: message.content, sent: true });


      // Enviar mensaje al servidor
      try {

        const apiUrl = process.env.VUE_APP_API_URL;
        const requestBody = {
          model: process.env.VUE_APP_MODEL,
          stream: false,
          messages:  this.messages.map(msg => ({ role: msg.sent ? 'user' : 'assistant', content: msg.content }))
        };

        console.log('Request body:', requestBody)
        const response = await axios.post(apiUrl, requestBody, { crossorigin: true });
        response.headers.set('Access-Control-Allow-Headers', 'Content-Type, Accept, Authorization');
        response.headers.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');

        // Agregar mensaje de respuesta del servidor
        this.messages.push({ content: response.data.message.content, sent: false });
        console.log('Respuesta del servidor:', response.data);
      } catch (error) {
        console.error('Error al enviar mensaje:', error);
      }
    }
  },
  mounted() {
    const container = this.$refs.app;
    container.addEventListener('scroll', this.handleScroll);
  },
  beforeUnmount() {
    // Elimina el listener para evitar fugas de memoria
    const container = this.$refs.app;
    container.removeEventListener('scroll', this.handleScroll);
  },
  components: {
    ChatInput,
    ChatMessages
  }
};
</script>

<style>
body {
  background-color: #333; /* Fondo oscuro para toda la página */
  color: #fff; /* Texto claro para toda la página */
}

#app {
  text-align: center;
  padding: 20px;
}

/* Estilos para el input y el botón */
input, button {
  background-color: #555; /* Fondo oscuro para el input y el botón */
  color: #fff; /* Texto claro para el input y el botón */
  border: none;
  padding: 10px;
  margin: 5px;
}

/* Estilos específicos para el botón */
button {
  cursor: pointer;
}
</style>

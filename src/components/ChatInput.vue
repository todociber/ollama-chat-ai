<template>
  <div class="chat-input-container">
    <input v-model="message" @keyup.enter="sendMessage" placeholder="Escribe tu mensaje..." />
    <button @click="sendMessage" class="send-button">➤</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: ''
    };
  },
  methods: {
    sendMessage() {
      if (this.message.trim() !== '') {
        this.$emit('new-message', { content: this.message, sent: true });
        this.message = '';
      }
    }
  }
};
</script>

<style scoped>
.chat-input-container {
  position: fixed;
  bottom: 0;
  align-items: center;
  alignment-baseline: center;
  width: 100%;
  text-align: center;
  background-color: #333;
  color: #fff;
  padding: 10px;
}

input {
  background-color: #555;
  color: #fff;
  border: 1px solid #ccc;
  padding: 10px;
  margin: 5px;
  width: 80%;
  border-radius: 20px; /* Ajusta el radio según tus preferencias para redondear el input */
}

.send-button {
  background-color: #555;
  color: #fff;
  border: none;
  padding: 10px;
  margin-left: 5px;
  cursor: pointer;
  border-radius: 50%; /* Ajusta el radio según tus preferencias para redondear el botón */
  font-size: 18px; /* Ajusta el tamaño de la flecha según tus preferencias */
}
</style>

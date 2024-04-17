<template>
  <v-app>
    <v-navigation-drawer v-model="drawer">
      <!-- Contenido del navigation drawer -->
    </v-navigation-drawer>

    <v-app-bar>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-app-bar-title>Application</v-app-bar-title>
    </v-app-bar>

    <v-main>
      <v-container v-show=true class="fill-width chatbot">
        <div class="message" v-for="message in messages" :key="message.id" :class="{ 'sent': message.sender === 'user', 'received': message.sender === 'bot' }">
          {{ message.text }}
        </div>
      </v-container>
      
      <v-container class="fill-width">
        <v-text-field v-model="userMessage" label="Mensaje" @keyup.enter="sendMessage"></v-text-field>
        <v-btn @click="sendMessage" class="send-button">Enviar</v-btn>
      </v-container>
    </v-main>
     
  </v-app>
</template>

<script setup>
  import { ref, onMounted   } from 'vue'
  import axios from 'axios'
  const drawer = ref(null)
</script>

<script>
export default {
  data() {
    return {
      drawer: null,
      messages: [],
      userMessage: ''
    };
  },
  methods: {
    sendMessage() {
      console.log('Mensaje enviado:', this.userMessage);
      // Agregar el mensaje del usuario al historial de mensajes
      this.messages.push({ id: this.messages.length + 1, text: this.userMessage, sender: 'user' });

      
       
      // Simular una respuesta del chatbot
      // const botResponse = this.generateBotResponse();
      // this.messages.push({ id: this.messages.length + 1, text: botResponse, sender: 'bot' });
      axios.get(`https://mywedding-backend.onrender.com/boda/${this.userMessage}`)
        .then(response => {
          console.log(response.data.palabras);
          // Agregar la respuesta del chatbot al historial de mensajes
          const botResponse = response.data.palabras;
          this.messages.push({ id: this.messages.length + 1, text: botResponse, sender: 'bot' });
          // Desplazar hacia abajo el contenedor de mensajes
        //  this.scrollToBottom();
        })
        .catch(error => {
          console.error('Error al llamar a la API del chatbot:', error);
        });

        this.userMessage = '';
    },
    generateBotResponse() {
      const responses = ['¡Hola! Soy un chatbot.', 'Gracias por tu mensaje.', '¿En qué puedo ayudarte?'];
      const randomIndex = Math.floor(Math.random() * responses.length);
      return responses[randomIndex];
    }
  }
};
</script>

<style scoped>
.chatbot {
  background-color: #f0f0f0;
  border-radius: 8px;
  padding: 20px;
  margin-top: 10px;
  height: 500px;
  margin-bottom: 20px;  
  overflow-y: auto;
}

.fill-width {
  width: calc(100% - 64px); 
  margin-right: auto;
  margin-left: auto;
}

.send-button {
  margin-top: 10px;  
}

.message {
  border-radius: 10px;
  padding: 10px;
  max-width: 70%; 
  word-wrap: break-word;  
}

.sent {
  background-color: #4CAF50;  
  color: white;
  text-align: right;
  margin-left: auto;
  margin-bottom: 15px;
}

.received {
  background-color: #2196F3;
  color: white;
  text-align: left;
  margin-bottom: 15px;
}
</style>

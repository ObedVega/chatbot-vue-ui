<template>
  <v-app>
    <v-navigation-drawer v-model="drawer">
      <!-- Contenido del navigation drawer -->
    </v-navigation-drawer>

    <v-app-bar>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-app-bar-title>Teradata QuickStart ChatBot</v-app-bar-title>
    </v-app-bar>

    <v-main>
      <v-container v-show=true class="fill-width chatbot">
        <div class="message" v-for="message in messages" :key="message.id" :class="{ 'sent': message.sender === 'user', 'received': message.sender === 'bot' }">
        
          <div v-html="formatMessageText(message.text)"></div>
        </div>
        <template v-if="loading">
          <v-progress-circular color="dark-blue" indeterminate></v-progress-circular>
          </template>
      </v-container>
            
      <v-container class="fill-width d-flex align-center">
        <v-text-field variant="outlined" class="text-b" v-model="userMessage" label="Ask a question" @keyup.enter="sendMessage"></v-text-field>
        <v-btn variant="plain"  @click="sendMessage" class="send-button" :disabled="!userMessage.trim()">Submit</v-btn>
      </v-container>
    </v-main>
     
  </v-app>
</template>

<script setup>
  import { ref  } from 'vue'
  import axios from 'axios'   
  const drawer = ref(null)
  
</script>

<script>
export default {
  mounted() {
    axios.defaults.headers.common['Access-Control-Allow-Origin'] = '*';
    const welcomeMessage = "Hello, I'm your Teradata Quickstart Assistant.";
    this.messages.push({ id: 1, text: welcomeMessage, sender: 'bot' });
  },
  data() {
    return {
      drawer: null,
      messages: [],
      userMessage: '',
      loading: false
    };
  },
  methods: {
    sendMessage() {
      this.loading = true;
      console.log('Mensaje enviado:', this.userMessage);
      // Agregar el mensaje del usuario al historial de mensajes
      this.messages.push({ id: this.messages.length + 1, text: this.userMessage, sender: 'user' });

      
       
      // Simular una respuesta del chatbot
      // const botResponse = this.generateBotResponse();
      // this.messages.push({ id: this.messages.length + 1, text: botResponse, sender: 'bot' });
      //axios.get(`http://localhost:3978/api/conversation/${this.userMessage}`)
      axios.post(`https://teradata-docs.azurewebsites.net/api/conversation`, { message: this.userMessage })
        .then(response => {
          console.log("Response: ",response.data);
          // Agregar la respuesta del chatbot al historial de mensajes
          const botResponse = response.data.message;
          this.messages.push({ id: this.messages.length + 1, text: botResponse, sender: 'bot' });
          // Desplazar hacia abajo el contenedor de mensajes
        //  this.scrollToBottom();
        })
        .catch(error => {
          console.error('Error al llamar a la API del chatbot:', error);
        })
        .finally(() => {
          this.loading = false;
          this.userMessage = '';
        });
    },
    formatMessageText(text) {
      // Inserta un salto de línea antes de la palabra "reference"
      const formattedText = text.replace(/\. reference/g, '.<br>reference');
      return formattedText;
    }
  }
};
</script>

<style scoped>
.text-format {
  font-size: 16px;
  line-height: 1.5;
}
.chatbot {
  background-color: #FFFFFF;
  border-radius: 8px;
  padding: 20px;
  margin-top: 10px;
  height: auto;
  margin-bottom: 20px;  
/*  overflow-y: auto; */
}

.fill-width {
  width: calc(100% - 64px); 
  margin-right: auto;
  margin-left: auto;
}

.send-button {
  /*border: none;
  background-color: transparent;
  padding: 0;
  font-size: inherit;
  cursor: pointer;*/
  color: #3053F4 !important;
  text-transform: none;
}

.message {
  border-radius: 8px;
  padding: 10px;
  max-width: 70%; 
  word-wrap: break-word;  
  gap: 16px;
}

.sent {
  background-color: #f0f0f0;  
  color: #000000;
  text-align: right;
  margin-left: auto;
  margin-bottom: 15px;
}

.received {
  background-color: #3053F4;
  color: white;
  text-align: left;
  margin-bottom: 15px;
}
.box{
  border-radius: 8px;
  border: 1px solid rgb(172, 166, 166);
}
.text-b{
  background-color: transparent !important;
}
</style>

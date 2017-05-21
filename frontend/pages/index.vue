<template>
  <div>
    <h1>Chat Application</h1>
    <ul>
      <li v-for="message in messages" class="chat-message">
        <strong>
          {{message.name}}
        </strong>
        <p>
          {{message.body}}
        </p>
      </li>
    </ul>
    <form @submit="postMessage">
      <input type="text" placeholder="Name" v-model="name">
      <textarea rows="5" placeholder="Write a message..." v-model="newMessage"></textarea>
      <button type="submit">Post Message</button>
    </form>
  </div>
</template>

<style>
  body {
    margin: 50px;
    font-family: sans-serif;
  }

  ul {
    margin: 0px;
    padding: 0px;
  }

  form input {
    border: 0 none;
    border-bottom: 1px solid #e5e5e5;
    display: block;
    margin: 20px 0;
    padding: 10px;
    width: 100%;
  }

  form textarea {
    border: 0 none;
    border-bottom: 1px solid #e5e5e5;
    display: block;
    padding: 10px;
    width: 100%;
  }

  .chat-message {
    border-bottom: 1px solid #e5e5e5;
    list-style: none;
    margin-bottom: 20px;
  }
</style>

<script>
  import axios from 'axios'
  import feathers from 'feathers/client'
  import socketio from 'feathers-socketio/client'
  import io from 'socket.io-client'

  export default {
    created() {
      axios.get('http://localhost:3030/messages').
        then((response) => {
          this.messages = response.data.data
        })
    },
    data() {
      return { 
        messages: [],
        name: '',
        newMessage: ''
      }
    },
    methods: {
      postMessage(e) {
        e.preventDefault()
        
        axios.post('http://localhost:3030/messages', {
          body: this.newMessage,
          name: this.name
        }).then(() => {
          this.newMessage = ''
        })
      }
    },
    mounted() {
      const app = feathers().configure(socketio(io('http://localhost:3030')))

      app.service('messages').on('created', (message) => {
        this.messages.push(message)
      })
    }
  }
</script>
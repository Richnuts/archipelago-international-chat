<template>
  <div id="app">
    <div class="chat-wrapper">
      <ChatWindow
        user="Excel"
        avatar="https://i.pravatar.cc/40?img=5"
        :messages="commonMessages"
        :typingUsers="getOtherTypingUsers('Excel')"
        @send="handleSendMessage"
        @typing="handleTypingStatus"
        inputPlaceholder="Type your message here..."
      />

      <ChatWindow
        user="Richap"
        avatar="https://i.pravatar.cc/40?img=6"
        :messages="commonMessages"
        :typingUsers="getOtherTypingUsers('Richap')"
        @send="handleSendMessage"
        @typing="handleTypingStatus"
        inputPlaceholder="Type your message here..."
      />
    </div>
  </div>
</template>

<script>
import ChatWindow from './components/ChatWindow.vue';

export default {
  name: 'App',
  components: { ChatWindow },
  data() {
    return {
      commonMessages: [
        {
          user: 'User 1',
          avatar: 'https://i.pravatar.cc/40?img=1',
          text: 'Do we have meeting today?'
        },
        {
          user: 'User 2',
          avatar: 'https://i.pravatar.cc/40?img=2',
          text: "I don't know why. Just move on."
        },
        {
          user: 'You', // This message will be "sent" by the "You" window
          avatar: 'https://i.pravatar.cc/40?img=5',
          text: "who's birthday is it?"
        },
        {
          user: 'User 1',
          avatar: 'https://i.pravatar.cc/40?img=1',
          text: 'beer friday, get the snacks'
        },
        {
          user: 'User 1',
          avatar: 'https://i.pravatar.cc/40?img=1',
          text: 'who brought the gifts 🎁?'
        },
        {
          user: 'User 3',
          avatar: 'https://i.pravatar.cc/40?img=3',
          text: 'I am Spartacus'
        }
      ],
      // Array to keep track of ALL users who are currently typing
      allTypingUsers: []
    };
  },
  methods: {
    handleSendMessage(message) {
      // Add the message to the common messages array
      this.commonMessages.push(message);
      // Immediately stop typing for the sender in the global typing list
      this.allTypingUsers = this.allTypingUsers.filter(u => u !== message.user);
    },
    handleTypingStatus({ user, typing }) {
      if (typing) {
        if (!this.allTypingUsers.includes(user)) {
          this.allTypingUsers.push(user);
        }
      } else {
        this.allTypingUsers = this.allTypingUsers.filter(u => u !== user);
      }
    },
    // This computed property/method ensures each chat window only sees *other* users typing
    getOtherTypingUsers(currentUser) {
      return this.allTypingUsers.filter(typer => typer !== currentUser);
    }
  }
};
</script>

<style>
/* Global styles or wrapper styles */
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #1f70c0;
  margin-top: 20px; /* Adjusted margin to give more space */
}

.chat-wrapper {
  display: flex;
  justify-content: center;
  gap: 20px; /* Space between the two chat windows */
  padding: 20px;
  background: #f0f2f5;
  min-height: 100vh;
  box-sizing: border-box;
  flex-wrap: wrap; /* Allow wrapping on smaller screens */
}
</style>
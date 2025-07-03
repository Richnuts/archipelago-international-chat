<template>
  <div class="chat-window">
    <div class="chat-header">
      <div class="chat-title"># general</div>
      <div class="chat-users-icon">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
          <path fill-rule="evenodd" d="M8.25 6.75a3.75 3.75 0 1 1 7.5 0 3.75 3.75 0 0 1-7.5 0ZM15.75 9.75a3 3 0 1 1-6 0 3 3 0 0 1 6 0ZM4.501 20.118a7.5 7.5 0 0 1 14.998 0A17.933 17.933 0 0 1 12 21.75c-2.676 0-5.216-.584-7.499-1.632Z" clip-rule="evenodd" />
        </svg>
      </div>
    </div>

    <div class="messages">
      <div v-for="(msg, index) in messages" :key="index" :class="['message-item', { 'sent': msg.user === user }]">
        <img :src="msg.avatar" alt="User Avatar" class="avatar" v-if="msg.user !== user" />
        <div class="message-content">
          <div class="message-user" v-if="msg.user !== user">{{ msg.user }}</div>
          <div class="message-bubble">{{ msg.text }}</div>
        </div>
      </div>
      <div class="typing-indicator-container">
        <template v-if="typingUsers.length > 0">
          <div v-for="typer in typingUsers" :key="typer" class="typing-indicator">
            <span class="typer-name">{{ typer }} is typing</span>
            <span class="dot-animation">...</span>
          </div>
        </template>
      </div>
    </div>

    <div class="message-input-area">
      <div class="input-field-wrapper">
        <input
          type="text"
          v-model="newMessage"
          @input="handleInput"
          @keyup.enter="sendMessage"
          :placeholder="inputPlaceholder"
        />
        <button @click.stop="toggleEmojiPicker" class="emoji-button-inside">☺</button>
      </div>

      <button @click="sendMessage" class="send-button">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
          <path d="M3.478 2.404a.75.75 0 0 0-.926.941l2.432 7.905H13.5a.75.75 0 0 1 0 1.5H4.984l-2.432 7.905a.75.75 0 0 0 .926.941L20.294 12 3.478 2.404Z" />
        </svg>
      </button>

      <div v-if="showEmojiPicker" class="emoji-picker-container" ref="emojiPickerRef">
        <emoji-picker @emoji-click="addEmoji"></emoji-picker>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue';
import 'emoji-picker-element'; // Import the web component library

export default {
  name: 'ChatWindow',
  props: {
    user: { type: String, required: true },
    avatar: { type: String, required: true },
    messages: { type: Array, default: () => [] },
    typingUsers: { type: Array, default: () => [] },
    inputPlaceholder: { type: String, default: 'Type a message...' }
  },
  setup(props, { emit }) {
    const newMessage = ref('');
    const showEmojiPicker = ref(false);
    const emojiPickerRef = ref(null);

    const sendMessage = () => {
      if (newMessage.value.trim()) {
        emit('send', {
          user: props.user,
          avatar: props.avatar,
          text: newMessage.value.trim(),
          timestamp: new Date().toISOString()
        });
        newMessage.value = '';
        showEmojiPicker.value = false;
      }
    };

    const handleInput = () => { /* ... (typing logic) ... */ };
    const scrollToBottom = () => {
      requestAnimationFrame(() => {
        const messagesContainer = document.querySelector('.messages');
        if (messagesContainer) {
          messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
      });
    };

    // --- Emoji Picker Logic ---
    const toggleEmojiPicker = () => {
      showEmojiPicker.value = !showEmojiPicker.value;
    };

    // THIS IS THE CORRECTED PART:
    const addEmoji = (event) => {
      // The `emoji-picker-element` sends the emoji as `event.detail.emoji.unicode`
      // or directly as `event.detail.unicode`. Let's use `unicode` directly.
      // Or, some versions might provide `event.detail.emoji.native` if it's nested differently.
      // Based on their common usage, `event.detail.unicode` is usually the simple emoji character.
      newMessage.value += event.detail.unicode; // Use event.detail.unicode
      showEmojiPicker.value = false; // Optionally close picker after selection
    };


    const handleClickOutside = (event) => {
      if (emojiPickerRef.value && !emojiPickerRef.value.contains(event.target) && !(event.target.classList.contains('emoji-button-inside'))) {
        showEmojiPicker.value = false;
      }
    };

    onMounted(() => {
      scrollToBottom();
      document.addEventListener('click', handleClickOutside);
    });

    onBeforeUnmount(() => {
      if (newMessage.value.trim().length > 0) {
        emit('typing', { user: props.user, typing: false });
      }
      document.removeEventListener('click', handleClickOutside);
    });

    watch(newMessage, (newVal, oldVal) => {
      const newHasText = newVal.trim().length > 0;
      const oldHasText = oldVal.trim().length > 0;
      if (newHasText && !oldHasText) {
        emit('typing', { user: props.user, typing: true });
      }
      else if (!newHasText && oldHasText) {
        emit('typing', { user: props.user, typing: false });
      }
    });

    watch(props.messages, () => { scrollToBottom(); }, { deep: true });

    return {
      newMessage,
      sendMessage,
      handleInput,
      scrollToBottom,
      showEmojiPicker,
      toggleEmojiPicker,
      addEmoji,
      emojiPickerRef
    };
  }
};
</script>

<style scoped>
/* All styles remain the same */

.chat-window {
  display: flex;
  flex-direction: column;
  width: 100%;
  max-width: 600px;
  height: 80vh;
  min-height: 400px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.chat-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 20px;
  background: #f8f8f8;
  border-bottom: 1px solid #eee;
}

.chat-title {
  font-weight: bold;
  color: #333;
}

.chat-users-icon svg {
  width: 24px;
  height: 24px;
  color: #777;
}

.messages {
  flex-grow: 1;
  padding: 15px 20px;
  overflow-y: auto;
  background-image: url(https://www.seekpng.com/png/detail/82-829667_save-inspiration-geometry-white-geometric-texture-png.png);
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.message-item {
  display: flex;
  align-items: flex-start;
  gap: 10px;
}

.message-item.sent {
  justify-content: flex-end;
}

.message-item .avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
}

.message-item.sent .avatar {
  display: none;
}

.message-content {
  display: flex;
  flex-direction: column;
  max-width: 70%;
  align-items: flex-start;
}

.message-user {
  font-size: 0.8em;
  color: #666666;
  margin-bottom: 2px;
}

.message-bubble {
  background: #e0e0e0;
  padding: 10px 15px;
  border-radius: 18px;
  word-wrap: break-word;
  color: #333;
  line-height: 1.4;
}

.message-item.sent .message-bubble {
  background: #007bff;
  color: white;
  align-self: flex-end;
}

.message-item.sent .message-user {
  display: none;
}

.message-input-area {
  display: flex;
  padding: 15px 20px;
  border-top: 1px solid #eee;
  background: white;
  align-items: center;
  gap: 10px;
}

.input-field-wrapper {
  flex-grow: 1;
  position: relative;
  display: flex;
  align-items: center;
}

.message-input-area input {
  flex-grow: 1;
  padding: 10px 15px;
  padding-right: 45px;
  border: 1px solid #007bff;
  border-radius: 20px;
  outline: none;
  font-size: 1em;
}

.emoji-button-inside {
  position: absolute;
  right: 8px;
  background: none;
  border: none;
  font-size: 1.4em;
  cursor: pointer;
  padding: 0;
  color: #777;
  line-height: 1;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
  transform: translateY(-2px);
}

.emoji-button-inside:hover {
  color: #000;
}

.send-button {
  background: #007bff;
  color: white;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background 0.2s ease;
  flex-shrink: 0;
}

.send-button:hover {
  background: #0056b3;
}

.send-button svg {
  width: 20px;
  height: 20px;
}

/* Typing Indicator Styles */
.typing-indicator-container {
  margin-top: 5px;
  min-height: 20px;
  text-align: left;
}

.typing-indicator {
  display: flex;
  align-items: center;
  font-size: 0.9em;
  color: #666;
  margin-top: 5px;
  font-style: italic;
}

.typer-name {
  margin-right: 3px;
  font-weight: bold;
}

/* Typing animation */
.dot-animation {
  overflow: hidden;
  display: inline-block;
  vertical-align: bottom;
  width: 15px;
}

.dot-animation::after {
  content: '';
  animation: typing-dots 1.5s infinite steps(1, start);
  animation-fill-mode: forwards;
}

@keyframes typing-dots {
  0% {
    content: '';
  }
  25% {
    content: '.';
  }
  50% {
    content: '..';
  }
  75% {
    content: '...';
  }
  100% {
    content: '...';
  }
}

/* Emoji Picker Container Styling */
.emoji-picker-container {
    position: absolute;
    bottom: 60px;
    right: 20px;
    z-index: 1000;
    box-shadow: 0 4px 20px rgba(0,0,0,0.2);
    border-radius: 8px;
    overflow: hidden;
    background: white;
}

/* Optional: Adjust default sizes of the web component if needed */
emoji-picker {
    --emoji-size: 28px;
    --font-size: 0.85rem;
    --width: 320px;
    --height: 400px;
}
</style>
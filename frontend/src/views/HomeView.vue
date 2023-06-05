
<template>
  <main>
    <div id="chat_container">
      <div
        v-for="(chat, i) in wrapper"
        :key="i"
        class="wrapper"
        :class="{ ai: chat.isAi }"
      >
        <Chat :chat="chat" :key="i" />
      </div>
    </div>
    <form @submit.prevent="fetchAnswer">
      <textarea
        rows="1"
        cols="1"
        placeholder="Type Your Contents..."
        v-model="question"
        @keypress.enter="fetchAnswer"
      >
      </textarea>
      <button type="submit"><img src="@/assets/send.svg" alt="send" /></button>
    </form>
  </main>
</template>

<script setup>
import { ref } from "vue";
import Chat from "../components/Chat.vue";

const question = ref("");
const wrapper = ref([]);
const loading = ref(false);

const fetchAnswer = async () => {
  try {
    loading.value = true;
    wrapper.value.push({
      isAi: false,
      value: question.value,
    });
    wrapper.value.push({
      isAi: true,
      value: "Loading...",
    });

    const res = await fetch("http://localhost:8000", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        question: question.value,
      }),
    });
    const data = await res.json();

    const parsedData = data.bot.trim(); // 从文本的开头和结尾删除前面的空格
    wrapper.value.pop(); // 移除加载状态
    wrapper.value.push({
      isAi: true,
      value: parsedData,
    });
  } catch (error) {
  } finally {
    loading.value;
    question.value = ""; // 清空输入框
  }
};
</script>

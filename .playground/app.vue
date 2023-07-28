<template>
  <div class="container">
    <h1>Nuxt Emoji Demo</h1>

    <p>Click to select an emoji</p>

    <div class="emoji-toggles">
      <NuxtEmoji :placement="'bottom'" :theme="'light'" @on-select="select" />
      <NuxtEmoji :placement="'right'" :theme="'dark'" @on-select="select">
        <template v-slot:button>
          <button class="dark-button">Emojis 😁</button>
        </template>
      </NuxtEmoji>
    </div>

    <div v-if="selected">
      <p>Selcted emoji:</p>
      <p style="font-size: 25px">{{ selected }}</p>
    </div>
  </div>
</template>

<style>
body {
  font-size: 16px;
  font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
    Helvetica Neue, Arial, Noto Sans, sans-serif, Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol,
    Noto Color Emoji;
}
</style>

<script setup lang="ts">
const selected = ref('')

function select(emoji: string) {
  selected.value = emoji
}
</script>

<style scoped>
.container {
  text-align: center;
  max-width: 500px;
  margin: 40px auto;
}
.dark-button {
  background: #000;
  border: 1px solid #000;
  color: #fff;
  font-size: 15px;
  border-radius: 4px;
  padding: 5px 10px;
}
.dark-button:hover {
  cursor: pointer;
}
.emoji-toggles {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  align-items: center;
}
</style>

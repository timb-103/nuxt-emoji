# Nuxt 3 Emoji Picker Component

An Emoji picker component for your Nuxt 3 project.

- ✅ Slot for a custom button
- 🔧 Props for theme, placement etc
- 🍪 Saves recently used into a cookie
- 🗨 Uses Tippy js to display picker ([vue-tippy](https://github.com/KABBOUCHI/vue-tippy))

![Nuxt Emoji](https://i.ibb.co/Gdhy9Wq/image-2023-07-28-165052223.png)

## Usage

Install the layer:

```sh
npm i -D nuxt-emoji
```

Add the layer in the `extends` array in `nuxt.config.ts`:

```js
export default defineNuxtConfig({
  extends: ['nuxt-emoji'],
})
```

Now you can use the emoji picker component anywhere in your project:

```vue
<template>
  <!-- Basic Usage -->
  <NuxtEmoji @on-select="select" />

  <!-- With Props -->
  <NuxtEmoji @on-select="select" :theme="light" :placement="bottom" />

  <!-- With Custom Button -->
  <NuxtEmoji @on-select="select">
    <template v-slot:button>
      <button>Custom Button 😁</button>
    </template>
  </NuxtEmoji>
</template>

<script setup>
function select(emoji) {
  // do something with your emoji
}
</script>
```

## Events

**`on-select`**

When the user clicks an emoji, we fire an on-select event that contains the emoji so you can do something with it.

## Slots

**`button`**

You can pass in a custom button using a `button` named slot. This will be the button that launches the component when clicked.

## Props

You can pass props to the component to change the style & placement. We'll continue to add on this as requested.

---

**`placement`**

Where the tippy popup showing the emoji picker will show. Options are:

- top-start
- top-end

- right
- right-start
- right-end

- bottom
- bottom-start
- bottom-end

- left
- left-start
- left-end

- auto
- auto-start
- auto-end

---

**`Theme`**

The main theme of component. Only options are `light` (default) and `dark`

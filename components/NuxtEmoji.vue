<template>
  <tippy
    trigger="click"
    :interactive="true"
    :placement="placement"
    :theme="theme"
    ref="tippyInstance"
  >
    <!-- Toggle Button -->
    <div
      class="emoji-toggle"
      v-tippy="{
        content: 'Emojis',
        touch: false,
        placement,
        theme,
        arrow: true,
      }"
    >
      <slot name="button">
        <button class="emoji-toggle-fallback">😀</button>
      </slot>
    </div>

    <!-- Content -->
    <template #content>
      <div class="emoji-container" @click.stop>
        <!-- Search -->
        <input type="text" v-model="filter" @input="update()" placeholder="Search..." />

        <!-- Emojis -->
        <div class="emoji-grid">
          <a
            v-for="(item, index) in filtered"
            :key="index"
            @click.prevent="select(item.emoji)"
            class="emoji"
          >
            {{ item.emoji }}
          </a>
        </div>

        <!-- Category Buttons -->
        <div class="emoji-button">
          <!-- Recent -->
          <div
            :key="'recent'"
            @click="changeGroup('recent')"
            :class="{ 'active-group': activeGroup === 'recent' }"
          >
            🕘
          </div>

          <!-- Other Categories -->
          <div
            v-for="(item, index) in emojis"
            :key="index"
            @click="changeGroup(String(index))"
            :class="{ 'active-group': activeGroup === index }"
          >
            {{ item[0].emoji }}
          </div>
        </div>
      </div>
    </template>
  </tippy>
</template>

<script setup lang="ts">
import EmojisList from '../assets/emojis'
import { TippyComponent } from 'vue-tippy'
import type { Placement } from '@popperjs/core'

defineProps({
  theme: {
    type: String as PropType<'dark' | 'light'>,
  },
  placement: {
    type: String as PropType<Placement>,
    required: false,
    default: () => 'top',
  },
})

interface Emoji {
  no: number
  code: string
  emoji: string
  description: string
  flagged: boolean
  keywords: string[]
}

interface Emojis {
  [key: string]: Emoji[]
}

const emits = defineEmits(['onSelect'])
const filter = ref('')
const emojis = ref<Emojis>(EmojisList)
const activeGroup = ref('Smileys & People')
const tippyInstance = ref<TippyComponent>()
const filtered = ref<Partial<Emoji>[]>()

/**
 * Update the emojis that are displayed.
 *
 * Fires on mount, on change group and when a user searches.
 */

async function update() {
  const all = Object.keys(emojis.value)

  // if recent group get cookie and parse into array
  if (activeGroup.value === 'recent' && !filter.value) {
    const recent = useCookie('recentEmojis')
    if (recent.value) {
      const recentFormatted = recent.value
        .split('_')
        .filter((v: any) => v)
        .map((v: any) => ({ emoji: v }))
        .reverse()

      filtered.value = recentFormatted
      return
    }
  }

  // return if no filter
  if (all.length && !filter.value) {
    filtered.value = emojis.value[activeGroup.value]
    return
  }

  // use filter to find emojis
  if (filter.value) {
    const temp: Emoji[] = []
    Object.keys(emojis.value).forEach((key) => {
      for (let i = 0; i < emojis.value[key].length; i += 1) {
        const item = emojis.value[key][i]
        if (
          item.description.includes(filter.value) ||
          item.keywords.some((x: string) => x.includes(filter.value))
        ) {
          temp.push(item)
        }
      }
    })
    filtered.value = temp
  }
}

/**
 * Change the active displayed category
 *
 * @param value - name of the emoji category
 */
function changeGroup(value: string) {
  activeGroup.value = value
  filter.value = ''
  update()
}

/**
 * Emits the selected Emoji & saves a cookie
 *
 * @param item - the emoji
 */
function select(item: any) {
  // emit the emoji
  emits('onSelect', item)

  // add the emoji into recently used cookie
  const recentEmojisCookie = useCookie('recentEmojis', { default: () => '' })
  recentEmojisCookie.value.replace(`_${item}`, '') // remove it if it exists
  recentEmojisCookie.value += `_${item}` // add it to the start

  // close the tippy
  tippyInstance.value?.hide()
}

onMounted(() => update())
</script>

<style scoped>
a {
  text-decoration: none;
}

/** Container */
.emoji-container {
  text-align: left;
  padding-top: 4px;
  max-height: 500px;
  overflow: auto;
}

/* Toggle Button */
.emoji-toggle {
  display: inline-block;
}
.emoji-toggle-fallback {
  background: #fff;
  border: 1px solid lightgrey;
  color: #8e8e8e;
  font-size: 15px;
  border-radius: 4px;
  aspect-ratio: 1;
}
.emoji-toggle-fallback:hover {
  background: #fafafa;
  cursor: pointer;
}

/** Emoji */
.emoji-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  height: 200px;
  overflow: auto;
  padding-top: 5px;
  align-content: start;
}
.emoji {
  font-size: 22px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow-x: hidden;
  width: 37px;
  height: 37px;
}
.emoji:hover {
  background: #fafafa;
  cursor: pointer;
}
.emoji-button {
  padding-top: 6px;
  border-top: 1px solid lightgrey;
  display: flex;
  justify-content: space-around;
  gap: 5px;
}
.emoji-button > div {
  border-radius: 4px;
  width: 27px;
  height: 27px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 17px;
}
.emoji-button > div:hover {
  background: #fafafa;
  cursor: pointer;
}
.active-group {
  background: lightgrey;
}

/** Filter Input  */
input {
  border-radius: 4px;
  box-sizing: border-box;
  border: 1px solid lightgrey;
  width: 100%;
  padding: 10px;
}
input:focus {
  box-shadow: none;
  outline: none;
}
</style>

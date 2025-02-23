<script setup lang="ts">
import { ref } from 'vue-demi'
import { toRefs } from '@vueuse/shared'
import Scrubber from '../../core/useMediaControls/components/Scrubber.vue'
import { useDrauu } from '.'

const colors = ref(['black', '#ef4444', '#22c55e', '#3b82f6'])
const target = ref()
const { undo, redo, canUndo, canRedo, clear, brush } = useDrauu(target, {
  brush: {
    color: 'black',
    size: 3,
  },
})

const { mode, color, size } = toRefs(brush)
</script>

<template>
  <div flex="~ col" place="items-center">
    <div
      shadow="~ lg"
      class="drauu-demo"
      border="rounded"
      overflow="hidden"
      max-w="screen-lg"
      h="[60vh]"
      w="full"
      flex="~ col"
    >
      <div
        bg="$c-bg"
        border="b-1 $c-divider"
        flex="~ row"
        items="center"
        p="2"
        space="x-4"
      >
        <div flex="~ row 1">
          <button
            v-for="_color in colors"
            :key="_color"
            :class="{ active: _color === color }"
            class="color-button"
            m="r-1"
            @click="() => color = _color"
          >
            <div :style="{ background: _color }" w="6" h="6" border="2 dark:(light-900 opacity-50) rounded-full" />
          </button>
        </div>
        <div flex="~ row 1 shrink-1" items="center" w="full" max-w="64">
          <carbon-paint-brush m="r-2" />
          <Scrubber v-model="size" w="full" :min="1" :max="10" />
        </div>
        <div flex="~ row 1" justify="end">
          <button class="tool-button" :disabled="!canUndo" @click="undo()">
            <carbon-undo />
          </button>
          <button class="tool-button" :disabled="!canRedo" @click="redo()">
            <carbon-redo />
          </button>
          <button class="tool-button" @click="clear()">
            <carbon-clean />
          </button>
        </div>
      </div>
      <div flex="~ row 1" h="72">
        <div
          bg="$c-bg"
          border="r-1"
          flex="~ col"
          space="y-2"
          place="items-center"
          p="2"
        >
          <button :class="{ active: brush.mode === 'draw' }" class="tool-button" @click="mode = 'draw'">
            <carbon-pen />
          </button>
          <button :class="{ active: brush.mode === 'line' && !brush.arrowEnd }" class="tool-button" @click="mode = 'line'">
            <mdi-slash-forward />
          </button>
          <button :class="{ active: brush.mode === 'rectangle' }" class="tool-button" @click="mode = 'rectangle'">
            <carbon-checkbox />
          </button>
          <button :class="{ active: brush.mode === 'ellipse' }" class="tool-button" @click="mode = 'ellipse'">
            <mdi-light-shape-circle />
          </button>
        </div>
        <svg
          ref="target"
          w="full"
          h="full"
          bg="white"
        />
      </div>
    </div>
  </div>
</template>

<style lang="postcss">
.drauu-demo .tool-button {
  @apply rounded-full m-0 bg-transparent text-dark-50 border-none h-8 w-8 p-0 flex place-items-center place-content-center;
}

.dark .drauu-demo .tool-button {
  @apply text-light-900;
}

.drauu-demo .tool-button:disabled {
  @apply text-light-900 bg-transparent border-none;
}

.dark .drauu-demo .tool-button:disabled {
  @apply text-dark-50;
}

.drauu-demo .tool-button:hover {
  @apply text-green-900;
}

.drauu-demo .tool-button.active {
  @apply bg-green-500 text-green-900;
}

.drauu-demo .color-button {
  @apply m-0 bg-transparent text-dark-50 rounded-full border-none h-8 w-8 p-0 flex place-items-center place-content-center;
}

.dark .drauu-demo .color-button {
  @apply ;
}

.drauu-demo .color-button:hover,
.drauu-demo .color-button.active {
  @apply bg-light-900;
}

.dark .drauu-demo .color-button:hover,
.dark .drauu-demo .color-button.active {
  @apply bg-dark-300;
}
</style>

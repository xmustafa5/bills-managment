<script setup lang="ts">
import { defineProps, defineEmits, onMounted, onUnmounted, computed, ref } from 'vue'

interface Props {
  title: string
  position?: 'left' | 'right'
  width?: string
}

const props = withDefaults(defineProps<Props>(), {
  position: 'left',
  width: '1000px',
})

const model = defineModel()

const closeDrawer = () => {
  model.value = false
}

const windowWidth = ref(window.innerWidth)

const handleResize = () => {
  windowWidth.value = window.innerWidth
}

onMounted(() => {
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
})

const drawerStyles = computed(() => {
  const requestedWidth = parseInt(props.width)

  return {
    width: windowWidth.value < requestedWidth ? '100%' : props.width,
  }
})
</script>

<template>
  <Teleport to="body">
    <Transition name="dialog-backdrop">
      <div v-if="model" class="drawer-container" @click.self="closeDrawer">
        <Transition name="dialog-content">
          <div
            v-if="model"
            class="drawer"
            :style="drawerStyles"
          >
            <div class="py-3 px-6 border-b border-gray-200 flex justify-between items-center">
              <span class="text-2xl">{{ title }}</span>
              <button class="close-button" @click="closeDrawer">×</button>
            </div>

            <div class="flex-1 py-3 px-6 overflow-y-auto">
              <slot></slot>
            </div>

            <div v-if="$slots.footer" class="py-3 px-6 border-t border-gray-200">
              <slot name="footer"></slot>
            </div>
          </div>
        </Transition>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.drawer-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.drawer {
  position: relative;
  max-height: 90vh;
  background-color: white;
  box-shadow: 0 4px 25px rgba(0, 0, 0, 0.15);
  display: flex;
  flex-direction: column;
  border-radius: 8px;
  overflow: hidden;
}

.drawer-right {
  right: 0;
  animation-name: slideInRight;
}

.drawer-left {
  left: 0;
  animation-name: slideInLeft;
}

.close-button {
  border: none;
  background: none;
  font-size: 24px;
  cursor: pointer;
  padding: 0;
  color: #666;
  transition: all 0.2s;
}

.close-button:hover {
  color: #000;
  transform: rotate(90deg);
}

/* Backdrop transition */
.dialog-backdrop-enter-active,
.dialog-backdrop-leave-active {
  transition: opacity 0.3s ease;
}

.dialog-backdrop-enter-from,
.dialog-backdrop-leave-to {
  opacity: 0;
}

/* Dialog content transition */
.dialog-content-enter-active,
.dialog-content-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.dialog-content-enter-from,
.dialog-content-leave-to {
  opacity: 0;
  transform: scale(0.9);
}

.dialog-content-enter-to,
.dialog-content-leave-from {
  opacity: 1;
  transform: scale(1);
}

/* Drawer animations */
@keyframes slideInRight {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0);
  }
}

@keyframes slideInLeft {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0);
  }
}

/* Transition animations still handling the show/hide */
.backdrop-leave-active .drawer-right {
  animation-name: slideOutRight;
}

.backdrop-leave-active .drawer-left {
  animation-name: slideOutLeft;
}

@keyframes slideOutRight {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(100%);
  }
}

@keyframes slideOutLeft {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-100%);
  }
}
</style>

<template>
    <div class="context-menu" :class="{ 'with-header': header }">
      <div v-if="header" class="menu-header">
        <div class="header-content">
          <h2 class="header-title">{{ header.header }}</h2>
          <p v-if="header.text" class="header-description">{{ header.text }}</p>
        </div>
      </div>
  
      <div class="menu-items" ref="menuItemsContainer">
        <menu-item
          v-for="(item, index) in items"
          :key="index"
          :item="item"
          :index="index"
          @select="$emit('select', index)"
        />
      </div>
  
      <transition name="fade">
        <image-preview
          v-if="previewImage"
          :image="previewImage"
          :position="previewPosition"
        />
      </transition>
    </div>
  </template>
  
  <script>
  import { ref, computed } from 'vue'
  import MenuItem from './MenuItem.vue'
  import ImagePreview from './ImagePreview.vue'
  
  export default {
    name: 'ContextMenu',
    components: { MenuItem, ImagePreview },
  
    props: {
      items: {
        type: Array,
        required: true
      },
      header: {
        type: Object,
        default: null
      }
    },
  
    setup() {
      const previewImage = ref(null)
      const previewPosition = ref({ x: 0, y: 0 })
      const menuItemsContainer = ref(null)
  
      const updatePreview = (image, event) => {
        if (!menuItemsContainer.value) return
  
        const rect = menuItemsContainer.value.getBoundingClientRect()
        previewImage.value = image
        previewPosition.value = {
          x: rect.left - 420,
          y: event.clientY - rect.top
        }
      }
  
      return {
        previewImage,
        previewPosition,
        menuItemsContainer,
        updatePreview
      }
    }
  }
  </script>

<style lang="scss">
.context-menu {
  width: var(--menu-width);
  background: var(--background-color);
  border-radius: var(--border-radius);
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  
  .menu-header {
    padding: 1rem;
    background: rgba(255, 255, 255, 0.05);
    border-bottom: 2px solid var(--primary-color);
    
    .header-title {
      font-size: 1.2rem;
      font-weight: 500;
      color: var(--text-color);
      margin-bottom: 0.25rem;
    }
    
    .header-description {
      font-size: 0.9rem;
      color: rgba(255, 255, 255, 0.7);
    }
  }
  
  .menu-items {
    max-height: 70vh;
    overflow-y: auto;
    padding: 0.5rem;
    
    &::-webkit-scrollbar {
      width: 5px;
    }
    
    &::-webkit-scrollbar-track {
      background: rgba(255, 255, 255, 0.1);
    }
    
    &::-webkit-scrollbar-thumb {
      background: var(--primary-color);
      border-radius: 10px;
    }
  }
}
</style>
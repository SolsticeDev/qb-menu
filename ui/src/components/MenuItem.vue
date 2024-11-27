<template>
    <div
      class="menu-item"
      :class="{
        'disabled': item.disabled,
        'with-icon': item.icon
      }"
      @click="handleClick"
      @mouseenter="handleMouseEnter"
      @mouseleave="handleMouseLeave"
    >
      <div v-if="item.icon" class="item-icon">
        <img
          :src="item.icon"
          :alt="item.header"
          @error="handleIconError"
        />
      </div>
  
      <div class="item-content">
        <div class="item-header">{{ item.header }}</div>
        <div v-if="item.text" class="item-description">{{ item.text }}</div>
      </div>
  
      <div class="item-indicator" v-if="!item.disabled">
        <svg viewBox="0 0 24 24" width="16" height="16">
          <path fill="currentColor" d="M8.59 16.59L13.17 12 8.59 7.41 10 6l6 6-6 6-1.41-1.41z"/>
        </svg>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'MenuItem',
  
    props: {
      item: {
        type: Object,
        required: true
      },
      index: {
        type: Number,
        required: true
      }
    },
  
    methods: {
      handleClick() {
        if (!this.item.disabled) {
          this.$emit('select', this.index)
        }
      },
  
      handleMouseEnter(event) {
        if (this.item.image) {
          this.$parent.updatePreview(this.item.image, event)
        }
      },
  
      handleMouseLeave() {
        this.$parent.updatePreview(null)
      },
  
      handleIconError(event) {
        event.target.style.display = 'none'
      }
    }
  }
  </script>

<style lang="scss">
.menu-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem;
  margin: 0.25rem 0;
  background: rgba(255, 255, 255, 0.05);
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: all var(--transition-duration) ease;
  
  &:not(.disabled):hover {
    background: var(--primary-color);
    transform: translateX(5px);
  }
  
  &.disabled {
    background: var(--disabled-color);
    cursor: not-allowed;
    opacity: 0.7;
  }
  
  .item-icon {
    width: 24px;
    height: 24px;
    flex-shrink: 0;
    
    img {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }
  }
  
  .item-content {
    flex: 1;
    
    .item-header {
      font-size: 0.95rem;
      color: var(--text-color);
      margin-bottom: 0.25rem;
    }
    
    .item-description {
      font-size: 0.8rem;
      color: rgba(255, 255, 255, 0.7);
    }
  }
  
  .item-indicator {
    color: var(--text-color);
    opacity: 0.5;
    transition: opacity var(--transition-duration) ease;
  }
  
  &:hover .item-indicator {
    opacity: 1;
  }
}
</style>

// styles for ImagePreview.vue
<style lang="scss">
.image-preview {
  position: absolute;
  width: 400px;
  background: var(--background-color);
  border-radius: var(--border-radius);
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  
  img {
    width: 100%;
    height: auto;
    max-height: 40vh;
    object-fit: contain;
  }
}
</style>
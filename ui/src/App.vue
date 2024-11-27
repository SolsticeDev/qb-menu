<template>
  <div id="app">
    <transition name="fade">
      <div v-if="isMenuVisible" class="menu-container">
        <div class="menu-panel">
          <!-- Animated Background Effect -->
          <div class="animated-bg">
            <div class="bg-gradient"></div>
            <div class="bg-pattern"></div>
          </div>
          
          <!-- Accent Lines -->
          <div class="accent-line top"></div>
          <div class="accent-line bottom"></div>

          <!-- Menu Content -->
          <div class="menu-content">
            <transition-group name="list" tag="div" class="items-container">
              <div
                v-for="(item, index) in filteredItems"
                :key="index"
                :id="index"
                :class="[
                  'menu-item',
                  {
                    'header': item.isMenuHeader,
                    'disabled': item.disabled,
                    'submenu': hasSubmenu(item)
                  }
                ]"
                @click="!item.isMenuHeader && !item.disabled && handleSelect(item, index)"
                @mouseenter="handleHover(item)"
                @mouseleave="handleHoverExit"
              >
                <!-- Interactive Background -->
                <div class="item-bg"></div>
                <div class="item-highlight"></div>
                <div class="item-border"></div>

                <!-- Icon Section -->
                <div v-if="item.icon" class="icon-wrap">
                  <div class="icon-ring"></div>
                  <i :class="item.icon"></i>
                  <div class="icon-glow"></div>
                </div>

                <!-- Content Section -->
                <div class="content-wrap">
                  <span class="title">{{ item.header }}</span>
                  <span v-if="item.txt" class="description">{{ item.txt }}</span>
                </div>

                <!-- Submenu Indicator -->
                <div v-if="hasSubmenu(item) && !item.disabled" class="submenu-icon">
                  <div class="arrow-wrap">
                    <svg viewBox="0 0 24 24" fill="none">
                      <path d="M9 5l7 7-7 7" stroke="currentColor" stroke-width="2" 
                        stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                  </div>
                </div>
              </div>
            </transition-group>
          </div>
        </div>

        <!-- Preview Panel -->
        <transition name="preview">
          <div v-if="hoveredImage" class="preview-panel">
            <div class="preview-content">
              <img :src="hoveredImage" alt="Preview">
              <div class="preview-overlay"></div>
              <div class="preview-shine"></div>
            </div>
          </div>
        </transition>
      </div>
    </transition>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Exo+2:ital,wght@0,100..900;1,100..900&family=Michroma&family=Orbitron:wght@400..900&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

:root {
  /* Colors */
  --menu-bg: rgba(20, 20, 30, 0.8);
  --item-bg: rgba(255, 255, 255, 0.03);
  --accent: rgb(220, 20, 60);
  --accent-glow: rgba(220, 20, 60, 0.15);
  --text: rgb(255, 255, 255);
  --text-secondary: rgba(255, 255, 255, 0.7);
  --border: rgba(255, 255, 255, 0.1);
  --shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  
  /* Effects */
  --blur: 12px;
  --transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.menu-container {
  position: fixed;
  top: 20%;
  right: 5%;
  display: flex;
  gap: 20px;
  z-index: 1000;
  font-family: "Orbitron", sans-serif;
  font-optical-sizing: auto;
  font-weight: 400;
  font-style: normal;
  /* Add this mask for the container */
  mask-image: linear-gradient(black, black);
  -webkit-mask-image: linear-gradient(black, black);
}

.menu-panel {
  position: relative;
  width: 440px;
  background: var(--menu-bg);
  border-radius: 20px;
  overflow: hidden;
  isolation: isolate;
  /* Move backdrop-filter to a pseudo-element */
}

.menu-panel::before {
  content: '';
  position: absolute;
  inset: 0;
  backdrop-filter: blur(var(--blur));
  -webkit-backdrop-filter: blur(var(--blur));
  border-radius: inherit;
  z-index: -1;
}

/* Animated Background */
.animated-bg {
  position: absolute;
  inset: 0;
  overflow: hidden;
  opacity: 0.5;
  border-radius: inherit;
  background: var(--menu-bg);
}

.bg-gradient {
  position: absolute;
  inset: -50%;
  background: conic-gradient(
    from 0deg at 50% 50%,
    var(--accent),
    rgba(255,255,255,0.1),
    var(--accent)
  );
  animation: rotate 15s linear infinite;
}

.bg-pattern {
  position: absolute;
  inset: 1px;
  background: var(--menu-bg);
  border-radius: inherit;
}

/* Menu Items Container */
.menu-content {
  position: relative;
  max-height: 75vh;
  overflow-y: auto;
  padding: 12px;
}

/* Menu Item */
.menu-item {
  position: relative;
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  margin: 8px 0;
  border-radius: 16px;
  cursor: pointer;
  overflow: hidden;
}

.item-bg {
  position: absolute;
  inset: 0;
  background: var(--item-bg);
  border-radius: inherit;
  z-index: -2;
}

.item-highlight {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    135deg,
    var(--accent-glow),
    transparent
  );
  opacity: 0;
  transition: opacity var(--transition);
  z-index: -1;
}

.item-border {
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: linear-gradient(
    135deg,
    var(--border),
    transparent
  );
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
}

/* Icon Styling */
.icon-wrap {
  position: relative;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.icon-ring {
  position: absolute;
  inset: 0;
  border: 1.5px solid var(--accent);
  border-radius: 12px;
  opacity: 0.3;
  transition: all var(--transition);
}

.icon-glow {
  position: absolute;
  inset: -2px;
  background: var(--accent-glow);
  border-radius: inherit;
  opacity: 0;
  transition: all var(--transition);
  filter: blur(8px);
}

.icon-wrap i {
  color: var(--accent);
  font-size: 1.2rem;
  z-index: 1;
  transition: transform var(--transition);
}

/* Content Styling */
.content-wrap {
  flex: 1;
}

.title {
  display: block;
  color: var(--text);
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: 4px;
}

.description {
  display: block;
  color: var(--text-secondary);
  font-size: 0.85rem;
  line-height: 1.4;
}

/* Submenu Icon */
.submenu-icon {
  color: var(--accent);
  opacity: 0.6;
  transition: all var(--transition);
}

/* Hover Effects */
.menu-item:not(.header):not(.disabled):hover {
  transform: translateX(8px);
}

.menu-item:hover .item-highlight {
  opacity: 1;
}

.menu-item:hover .icon-ring {
  transform: scale(1.1);
  opacity: 1;
}

.menu-item:hover .icon-glow {
  opacity: 0.8;
}

.menu-item:hover .icon-wrap i {
  transform: scale(1.1);
}

.menu-item:hover .submenu-icon {
  opacity: 1;
  transform: translateX(4px);
}

/* Header Styling */
.menu-item.header {
  backdrop-filter: blur(8px);
  border-bottom: 2px solid var(--accent);
  cursor: default;
}

.menu-item.header .title {
  font-size: 1.2rem;
  font-weight: 700;
  letter-spacing: 0.5px;
}

/* Disabled State */
.menu-item.disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Animations */
@keyframes rotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity var(--transition);
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.list-enter-active,
.list-leave-active {
  transition: all var(--transition);
}

.list-enter-from {
  opacity: 0;
  transform: translateX(30px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}

/* Preview Panel */
.preview-panel {
  width: 440px;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: var(--shadow);
}

.preview-content {
  position: relative;
  width: 100%;
}

.preview-content img {
  width: 100%;
  height: auto;
  max-height: 50vh;
  object-fit: cover;
}

.preview-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 50%;
  background: linear-gradient(
    to top,
    rgba(0, 0, 0, 0.8),
    transparent
  );
}

.preview-shine {
  position: absolute;
  top: 0;
  left: -100%;
  width: 50%;
  height: 100%;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.2),
    transparent
  );
  animation: shine 3s infinite;
}

@keyframes shine {
  to {
    left: 200%;
  }
}

/* Scrollbar */
.menu-content::-webkit-scrollbar {
  width: 4px;
}

.menu-content::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.05);
}

.menu-content::-webkit-scrollbar-thumb {
  background: linear-gradient(to bottom, var(--accent), rgba(220, 20, 60, 0.5));
  border-radius: 2px;
}
</style>

<script>
export default {
  name: 'App',
  
  data() {
    return {
      isMenuVisible: false,
      menuItems: [],
      hoveredImage: null,
      isSubmenu: false
    }
  },

  computed: {
    filteredItems() {
      return this.menuItems.filter(item => {
        if (this.isReturnButton(item)) {
          return this.isSubmenu
        }
        return !item.hidden
      })
    }
  },

  methods: {
    isReturnButton(item) {
      return item.header && (
        item.header.includes('Return') || 
        item.header.includes('Go Back') ||
        item.header.includes('←')
      )
    },

    hasSubmenu(item) {
      return item.params?.event?.toLowerCase().includes('menu')
    },

    handleMessage(event) {
      const { action, data } = event.data
      switch (action) {
        case 'OPEN_MENU':
        case 'SHOW_HEADER':
          this.openMenu(data)
          break
        case 'CLOSE_MENU':
          this.closeMenu()
          break
      }
    },

    openMenu(data) {
      if (!data || !data.length) return
      
      this.isSubmenu = data.some(item => this.isReturnButton(item))
      this.menuItems = data
      this.isMenuVisible = true
    },

    closeMenu() {
      this.isMenuVisible = false
      this.hoveredImage = null
      this.menuItems = []
      
      fetch(`https://${GetParentResourceName()}/closeMenu`, {
        method: 'POST',
        body: JSON.stringify({})
      }).catch(() => {})
    },

    handleSelect(item, index) {
      if (item.disabled || item.isMenuHeader) return

      fetch(`https://${GetParentResourceName()}/clickedButton`, {
        method: 'POST',
        body: JSON.stringify(index + 1)
      })
      .then(() => {
        if (!this.hasSubmenu(item)) {
          this.closeMenu()
        }
      })
      .catch(() => {})
    },

    handleHover(item) {
      if (item.image) {
        this.hoveredImage = item.image
      }
    },

    handleHoverExit() {
      this.hoveredImage = null
    }
  },

  mounted() {
    window.addEventListener('message', this.handleMessage)
    window.addEventListener('keyup', e => {
      if (e.key === 'Escape' && this.isMenuVisible) {
        this.closeMenu()
      }
    })
  },

  beforeUnmount() {
    window.removeEventListener('message', this.handleMessage)
  }
}
</script>
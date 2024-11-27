<template>
    <div class="image-preview" :style="previewStyle">
      <img :src="image" alt="Preview" />
    </div>
  </template>
  
  <script>
  export default {
    name: 'ImagePreview',
  
    props: {
      image: {
        type: String,
        required: true
      },
      position: {
        type: Object,
        required: true
      }
    },
  
    computed: {
      previewStyle() {
        return {
          top: `${this.position.y}px`,
          left: `${this.position.x}px`
        }
      }
    }
  }
  </script>
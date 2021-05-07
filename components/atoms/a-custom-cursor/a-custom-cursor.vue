<template>
  <div>
    <div ref="cursor" class="cursor outter-cursor"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      elCursor: null,
      clientX: 0,
      clientY: 0,
      cursorSpeed: 0,
      lastTime: 0,
      lastX: 0,
      lastY: 0,
      rotation: 0,
    }
  },
  mounted() {
    this.clientX = -100
    this.clientY = -100
    document.addEventListener('mousemove', this.mouseMoved)
    this.elCursor = this.$refs.cursor
    this.initCursor()
    this.render()
  },
  methods: {
    initCursor() {
      this.$gsap.set(this.elCursor, {
        x: this.clientX,
        y: this.clientY,
      })
      setTimeout(() => {
        this.cursorSpeed = 0.5
      }, 100)
    },
    mouseMoved(e) {
      this.clientX = e.clientX
      this.clientY = e.clientY
    },
    render() {
      this.$gsap.to(
        this.elCursor,
        {
          x: this.clientX,
          y: this.clientY,
        },
        this.outerCursorSpeed
      )
      requestAnimationFrame(this.render)
    },
  },
}
</script>

<style lang="scss">
@import './a-custom-cursor.scss';
</style>

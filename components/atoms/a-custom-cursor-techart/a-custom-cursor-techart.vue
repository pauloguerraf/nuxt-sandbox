<template>
  <div>
    <div class="cursor">
      <div ref="outer-wrapper" class="outer-wrapper">
        <div ref="outer-circle" class="outer-circle">
          <div ref="outer-circle-inner" class="outer-circle-inner">
            <div class="outer-circle-inner-1"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      innerCircle: null,
      innerWrapper: null,
      outerCircle: null,
      outerWrapper: null,
      outerCircleInner: null,
      mousePos: { x: 0, y: 0 },
      lastMousePos: { x: 0, y: 0 },
      lastVelocityPos: { x: 0, y: 0 },
      innerPos: { x: 0, y: 0 },
      outerPos: { x: 0, y: 0 },
      velocity: { x: 1, y: 1 },
      mouseAngle: 0,
      mouseVelocityRate: 50,
      mouseResetTimer: 0,
      mousePosInterval: 0,
    }
  },
  mounted() {
    this.outerCircle = this.$refs['outer-circle']
    this.outerCircleInner = this.$refs['outer-circle-inner']
    this.outerWrapper = this.$refs['outer-wrapper']
    document.addEventListener('mousemove', this.handleMouseMove)
    this.mousePosInterval = setInterval(() => {
      this.lastVelocityPos.x = this.mousePos.x
      this.lastVelocityPos.y = this.mousePos.y
    }, this.mouseVelocityRate)
    requestAnimationFrame(this.handleTick)
  },
  methods: {
    handleTick() {
      this.updateRotation()
      this.updatePositions(0.5)
      this.updateTranslation()
      requestAnimationFrame(this.handleTick)
    },
    handleMouseMove(t) {
      this.lastMousePos.x = this.mousePos.x
      this.lastMousePos.y = this.mousePos.y
      this.mousePos.x = t.clientX
      this.mousePos.y = t.clientY
      this.updateVelocity()
      this.mouseResetTimer = setTimeout(
        () => this.updateVelocity(),
        this.mouseVelocityRate
      )
    },
    updateRotation() {
      const t = this.mousePos.x - this.lastMousePos.x
      const e = this.mousePos.y - this.lastMousePos.y
      const i = Math.atan2(e, t)
      const s = (Math.abs(i - this.mouseAngle) / Math.PI) * 180 < 145 ? 0.2 : 1
      this.mouseAngle = this.lerp(this.mouseAngle, i, s)
    },
    updateVelocity() {
      const t = this.lastVelocityPos.x - this.mousePos.x
      const e = this.lastVelocityPos.y - this.mousePos.y
      const i = this.u(t / this.mouseVelocityRate, -10, 10)
      const s = this.u(e / this.mouseVelocityRate, -10, 10)
      this.$gsap.fromTo(this.velocity, { x: i, y: s }, { duration: 500 })
    },
    updatePositions(t) {
      this.outerPos.x = this.lerp(this.outerPos.x, this.mousePos.x, 0.1 * t)
      this.outerPos.y = this.lerp(this.outerPos.y, this.mousePos.y, 0.1 * t)
    },
    updateTranslation() {
      const e = this.outerPos
      this.outerWrapper.style.transform = `translate3d(${e.x}px,${e.y}px,0)`
      const i = Math.min(
        Math.max(
          Math.abs(1.9 * this.velocity.x),
          Math.abs(1.9 * this.velocity.y)
        ),
        1
      )
      this.outerCircle.style.transform = `rotate(${this.mouseAngle}rad)`
      this.outerCircleInner.style.transform = `scaleX(${1 + i * 1.3})`
      this.outerCircleInner.style.filter = `blur(${i * 3}px)`
      this.outerCircleInner.style.webkitFilter = `blur(${i * 3}px)`
    },
    lerp(start, end, amt) {
      return start * (1 - amt) + end * amt
    },
    u(t, e, i) {
      return Math.min(Math.max(t, e), i)
    },
  },
}
</script>

<style lang="scss">
@import './a-custom-cursor-techart.scss';
</style>

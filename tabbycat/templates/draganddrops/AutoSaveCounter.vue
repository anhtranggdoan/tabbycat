<template>

  <!-- ADD tooltip -->
  <button :class="['btn btn-link border-primary auto-save', customClasses, animationClass]"
          data-toggle="tooltip"
          data-placement="bottom" title="Changes are automatically saved; however do not edit/change
                                         allocations across multiple browsers/computers at the
                                         same time!">
    <span :class="[animationClass]">{{ savedAgoDisplay }}</span>
  </button>

</template>

<script>
export default {
  data: function () { return { lastSaved: false, animationClass: '' } },
  props: { css: String },
  created: function () {
    this.$eventHub.$on('update-saved-counter', this.updateLastSaved)
  },
  methods: {
    updateLastSaved: function () {
      this.lastSaved = new Date()
      this.animationClass = 'save-flash'
      setTimeout(() => { this.animationClass = '' }, 5000)
      setInterval(() => {
        // Slightly increment to trigger savedAgoDisplay updates
        this.lastSaved = new Date(this.lastSaved.getTime() + 0.001)
      }, 1000)
    },
  },
  computed: {
    savedAgoDisplay: function () {
      if (!this.lastSaved) { return 'No Changes' }
      const savedAgo = Math.abs(new Date() - this.lastSaved) / 1000
      if (savedAgo > 59) {
        return `Saved at ${this.hours}:${this.minutes}`
      }
      return `Saved ${parseInt(savedAgo)}s ago`
    },
    customClasses: function () {
      return this.css
    },
    hours: function () {
      return this.lastSaved.getHours()
    },
    minutes: function () {
      const minutes = String(this.lastSaved.getMinutes())
      if (minutes.length === 1) {
        return `0${minutes}`
      }
      return minutes
    },
  },
}
</script>

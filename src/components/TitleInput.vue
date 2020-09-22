<template>
  <b-modal
    centered
    title="Enter new task list name"
    hide-footer
    :header-text-variant="darkMode ? 'light' : 'dark'"
    :header-bg-variant="darkMode ? 'dark' : 'light'"
    :body-bg-variant="darkMode ? 'dark' : 'light'"
    :id="id"
  >
    <b-input
      autofocus
      type="text"
      v-model="newTitle"
    ></b-input>
    <b-button
      block
      class="mt-3"
      :variant="darkMode ? 'outline-light' : 'outline-dark'"
      @click="handleTitleInput"
    >
      OK
    </b-button>
  </b-modal>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'TitleInput',
  props: {
    id: String,
    darkMode: Boolean,
    addMode: Boolean
  },
  data () {
    return {
      newTitle: ''
    }
  },
  methods: {
    handleTitleInput (): void {
      if (this.newTitle && !this.addMode) {
        this.$emit('set-new-title', this.newTitle)
      } else if (this.newTitle && this.addMode) {
        this.$emit('set-new-list-title', this.newTitle)
      }
      this.newTitle = ''
      this.$bvModal.hide(this.id)
    }
  }
})
</script>

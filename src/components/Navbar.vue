<template>
  <b-navbar
    toggleable="sm"
    :variant="darkMode ? 'dark' : 'light'"
    :type="darkMode ? 'dark' : 'light'"
  >
    <b-navbar-brand href="#">
      <b-icon icon="check2-square" class="mr-2"></b-icon>
      <span id="calendar-title">TODU</span>
    </b-navbar-brand>
    <b-nav-toggle target="nav-collapse">
      <b-icon icon="list" aria-hidden="true"></b-icon>
    </b-nav-toggle>
    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav class="ml-auto">
        <b-nav-form>
          <b-button
            id="add-list-button"
            class="mr-2"
            :variant="darkMode ? 'dark' : 'light'"
            @click="$emit('add-list')"
          >
            <b-icon
              aria-hidden
              :icon="darkMode ? 'bookmark-plus-fill' : 'bookmark-plus'"
            ></b-icon>
          </b-button>
          <b-button
            id="dark-mode-shift-button"
            class="mr-2"
            :variant="darkMode ? 'dark' : 'light'"
            @click="$emit('shift-dark-mode')"
          >
            <b-icon
              aria-hidden
              :icon="darkMode ? 'moon' : 'sun'"
            ></b-icon>
          </b-button>
        </b-nav-form>
      </b-navbar-nav>
    </b-collapse>
  </b-navbar>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'Navbar',
  props: {
    darkMode: Boolean
  }
})
</script>

<style scoped>
.navbar {
  width: 100%;
}
#add-list-button {
  width: 30pt;
  height: 30pt;
  border-radius: 30pt;
  padding: 0;
  vertical-align: text-top;
}
#dark-mode-shift-button {
  width: 30pt;
  height: 30pt;
  border-radius: 30pt;
  padding: 0;
  vertical-align: text-top;
}
.change-date-button {
  width: 30pt;
  height: 30pt;
  border-radius: 30pt;
  padding: 0;
}
</style>

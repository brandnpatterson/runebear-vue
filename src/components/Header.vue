<template>
  <nav class="navbar" aria-label="main navigation">
    <div
      v-on:click="toggleActive"
      v-bind:class="['navbar-burger is-large', { 'is-active': isActive }]"
      data-target="navMenu"
    >
      <span />
      <span />
      <span />
    </div>
    <div v-bind:class="['navbar-menu navbar-target', { 'is-active': isActive }]" id="navMenu">
      <div class="navbar-items">
        <div v-for="title in header" :key="title">
          <router-link :to="title === 'Home' ? '/' : `/${title}`">{{ title }}</router-link>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
import { dark, light } from '../util/color'
import { mediumUp } from '../util/media'

export default {
  name: 'Header',
  props: {
    header: Array
  },
  data() {
    return {
      isActive: false,
      dark, light, medumUp
    }
  },
  methods: {
    toggleActive() {
      if (window.innerWidth <= 1023) {
        this.isActive = !this.isActive;
      }
    }
  },
  beforeMount() {
    this.toggleActive();
  }
};
</script>

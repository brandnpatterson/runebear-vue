<template>
  <div class="home">
    <h2>{{ count }}</h2>
    <button class="button is-primary">{{ message }}</button>
  </div>
</template>

<script>
export default {
  name: 'home',
  data() {
    return {
      count: 0,
      message: 'Click Me'
    }
  }
};
</script>

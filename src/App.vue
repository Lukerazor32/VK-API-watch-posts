<template>
  <div id="app">
    <h1>VK API POSTS</h1>
    <PostLoop/>
  </div>
</template>

<script>
import PostLoop from './PostLoop';

export default {
  components: {
    PostLoop
  }
};
</script>

<style>
  h1 {
    text-align: center;
  }
</style>
<template>
  <v-app>
    <h1 class="display-2 text-xs-center mt-4">埼玉県のイベント</h1>
    <v-content>
      <Events/>
    </v-content>
    <div class=footer>
      <div>App: by <a href=https://code4fukui.github.io/>Code for Fukui</a> (<a href=https://github.com/code4fukui/SaitamaEvents>src on GitHub</a>)</div>
      <div>Base App: <a href=https://watano1168.github.io/FukuiEvents/>福井県のイベント</a> (<a href=https://github.com/watano1168/FukuiEvents>src on GitHub</a>)</div>
    </div>
  </v-app>
</template>

<style>
.footer {
  text-align: center;
}
</style>

<script>
import Events from './components/Events'

export default {
  name: 'App',
  components: {
    Events
  }
}
</script>

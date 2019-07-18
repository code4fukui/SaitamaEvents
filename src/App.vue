<template>
  <v-app>
    <title>オープンイベント埼玉 - 埼玉県広報情報オープンデータ</title>
    <h1 class="display-2 text-xs-center mt-4">オープンイベント埼玉</h1>
    <v-content>
      <Events/>
    </v-content>
    <div class=footer>
      <div>App: by <a href=https://code4fukui.github.io/>Code for Fukui</a> (<a href=https://github.com/code4fukui/SaitamaEvents>src on GitHub</a>)</div>
      <div>Data: CC BY <a href=https://opendata.pref.saitama.lg.jp/data/dataset/9b283d97-f53e-4346-b734-b43695eac1c9>【埼玉県】広報情報</a> by <a href=https://opendata.pref.saitama.lg.jp/>埼玉県オープンデータポータルサイト</a></div>
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

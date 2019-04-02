<template>
  <div id="app">
    <editor @update="handleText" :text="text"></editor>
    <viewer :text="text"></viewer>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Editor from './components/Editor.vue';
import Viewer from './components/Viewer.vue';
import { text } from './TXT_JOURNAL_BIENNALE_BRUT';

@Component({
  components: {
    Editor,
    Viewer,
  },
})
export default class App extends Vue {
  public text: string = text;

  private handleText(e: string) {
    this.text = e;
  }
}
</script>

<style lang="scss">
  html, body {
    margin: 0;
    padding: 0;
  }

  html {
    font-family: sans-serif;
    font-size: 16px;
    line-height: 1.2;
    background: #03294f;
  }

  html * {
    box-sizing: border-box;
  }

  #app {
    display: flex;
    width: 100%;
    height: 100vh;
    overflow: hidden;
  }
</style>

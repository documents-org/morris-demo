<template>
  <div class="Editor">
    <textarea cols="30" rows="10" @input="handleInput">
      {{ text }}
    </textarea>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Editor extends Vue {
  @Prop() private text!: string;
  private handleInput(event: KeyboardEvent) {
    const t = event.target as HTMLTextAreaElement;
    this.$emit('update', t.value);
  }
}
</script>

<style lang="scss">
  .Editor {
    flex: 0 0 35%;
    padding: 1em;
    textarea {
      border-radius: 2px;
      border: none;
      padding: 1em;
      width: 100%;
      height: 100%;
    }
  }
</style>

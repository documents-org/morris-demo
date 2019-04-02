<template>
  <div class="Viewer">
    <div class="Controls" style="font-size:11px;">
      <div><label for="">Formater avec Morris <input type="checkbox" v-model="doFormat"></label></div>
      <div><label for="">Afficher les invisibles <input type="checkbox" v-model="doShowInvisibles"></label></div>
      <div><label for="">Colonne <input type="range" v-model="columnWidth"></label></div>
      <div><label for="">Taille du texte <input type="range" v-model="fontSize"></label></div>
      <div><label for="">Interligne <input type="range" v-model="lineHeight"></label></div>
      <hr>
      <div :style="{pointerEvents: doFormat ? 'initial' : 'none', opacity: doFormat ? 1 : 0.5}">
        Règles
        <div v-for="rule in rules" style="margin-top: .25em;">
          <label for="">
            <input type="checkbox" :checked="usesRule(rule.id)" @click="toggleRule(rule.id)" style="margin-right: .5em;">
            <span style="display: inline-block;width: 180px;">{{ rule.description }}</span>
          </label>
        </div>
      </div>
    </div>
    <div style="height: 100%; flex: 1; justify-content: center">
      <p v-html="format(text)" :style="{ maxWidth: `${columnWidth}ch`, fontSize: `${fontSize}px`, lineHeight: lineHeight / 10 }"></p>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import William from 'morris';

@Component({computed: {
    rules(): any[] {
      return William.getRules;
    },
  }})
export default class Viewer extends Vue {

  private get usedRules(): number[] {
    return Object.entries(this.ruleMap)
            .filter((a) => a[1])
            .map((a) => parseInt(a[0], 10));
  }

  public doFormat: boolean = true;
  public doShowInvisibles: boolean = true;
  public columnWidth: number = 67;
  public fontSize: number = 16;
  public lineHeight: number = 10;
  public ruleMap: any = William.getRules.reduce((acc: any, r: any) => { acc[r.id] = true; return acc; }, {});
  @Prop() private text!: string;

  private usesRule(id: number): boolean {
    if (typeof this.ruleMap[id] !== 'undefined') {
      return this.ruleMap[id];
    }
    return false;
  }

  private toggleRule(id: number) {
    if (typeof this.ruleMap[id] !== 'undefined') {
      this.ruleMap[id] = !this.ruleMap[id];
    } else {
      this.ruleMap[id] = true;
    }
  }

  private format(str: string): string {
    let f = this.text;
    if (this.doFormat) {
      f = this.usedRules.reduce((text, ruleId) => William.apply(text, 'brut', ruleId), f);
      f = this.usedRules.reduce((text, ruleId) => William.apply(text, 'html', ruleId), f);
    }
    if (this.doShowInvisibles) { f = this.addInvisibles(f); }
    return f;
  }

  private addInvisibles(str: string) {
    const find = /\s/gi;
    const replace = (match: string) => {
      const a: string | undefined = ({
        '\u0009': 'character_tabulation' ,
        '\u000A': 'line_feed' ,
        '\u000B': 'line_tabulation' ,
        '\u000C': 'form_feed' ,
        '\u000D': 'carriage_return' ,
        '\u0020': 'space' ,
        '\u0085': 'next_line' ,
        '\u00A0': 'no_break_space' ,
        '\u1680': 'ogham_space_mark' ,
        '\u2000': 'en_quad' ,
        '\u2001': 'em_quad' ,
        '\u2002': 'en_space' ,
        '\u2003': 'em_space' ,
        '\u2004': 'three_per_em_space' ,
        '\u2005': 'four_per_em_space' ,
        '\u2006': 'six_per_em_space' ,
        '\u2007': 'figure_space' ,
        '\u2008': 'punctuation_space' ,
        '\u2009': 'thin_space' ,
        '\u200A': 'hair_space' ,
        '\u2028': 'line_separator' ,
        '\u2029': 'paragraph_separator' ,
        '\u202F': 'narrow_no_break_space' ,
        '\u3000': 'ideographic_space' ,
        '\u200B': 'zero_width_space' ,
        '\u200C': 'zero_width_non_joiner' ,
        '\u200D': 'zero_width_joiner' ,
        '\u2060': 'word_joiner' ,
        '\uFEFF': 'zero_width_non_breaking',
      } as any)[match];
      if (typeof a !== 'undefined') { return `<span class="invis-${a as string}">${match}</span>`; }
      return match;
    };
    return str.replace(find, replace);
  }
}
</script>

<style lang="scss">
  .Viewer {
    flex: 0 0 65%;
    display: flex;
    height: 100%;
    overflow-y: scroll;
    background: #d7dec7;
    .Controls {
      background: #666;
      padding: 1em;
      color: white;
    }
    p {
      font-family: Arial, sans-serif;
      overflow-y: scroll;
      padding: 2em;
      margin: auto;
      height: 100%;
      background: whitesmoke;
      span {
        background: #bbbbbb;
        position: relative;
        opacity: 0.65;
        &.invis {
          &-no_break_space {
            background: pink;

          }
          &-narrow_no_break_space {
            background: lightgreen;
          }

          &-thin_space {
            background: lightblue;
          }
        }
      }
    }
  }
</style>

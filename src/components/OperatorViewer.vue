<template>
  <svg class="operator-viewer" :width="width" :height="height">
      <g>
        <rect
        v-for="(d, i) in matrixSparse"
        :key="i"
        class="tile"
        :x="scale(d.i)"
        :y="scale(d.j)"
        :width="size"
        :height="size"
        :style="{fill: colorComplex(d.re, d.im)}"
        @mouseover="tileMouseOver(d)"
      />
      </g>
  </svg>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { scaleLinear } from 'd3-scale'
import * as qt from 'quantum-tensors'

const TAU = 2 * Math.PI

function generateData(n: number) {
  return (new Array(n * n))
    .fill(undefined)
    .map((x, i) => {
      return {
        i: i % n,
        j: Math.floor(i / n),
        re: 2 * Math.random() - 1,
        im: 2 * Math.random() - 1,
      }
    })
}

const nForTest = 8



/**
 * Stolen from https://stackoverflow.com/questions/36721830/convert-hsl-to-rgb-and-hex
 * Alternatively: d3.hsl
 */
function hslToHex(h: number, s: number, l: number) {
  h /= 360;
  s /= 100;
  l /= 100;
  let r, g, b;
  if (s === 0) {
    r = g = b = l; // achromatic
  } else {
    const hue2rgb = (p: number, q: number, t: number) => {
      if (t < 0) t += 1;
      if (t > 1) t -= 1;
      if (t < 1 / 6) return p + (q - p) * 6 * t;
      if (t < 1 / 2) return q;
      if (t < 2 / 3) return p + (q - p) * (2 / 3 - t) * 6;
      return p;
    };
    const q = l < 0.5 ? l * (1 + s) : l + s - l * s;
    const p = 2 * l - q;
    r = hue2rgb(p, q, h + 1 / 3);
    g = hue2rgb(p, q, h);
    b = hue2rgb(p, q, h - 1 / 3);
  }
  const toHex = (x: number) => {
    const hex = Math.round(x * 255).toString(16);
    return hex.length === 1 ? '0' + hex : hex;
  };
  return `#${toHex(r)}${toHex(g)}${toHex(b)}`;
}


@Component
export default class OperatorViewer extends Vue {
  @Prop({default: () => 800}) private width!: number
  @Prop({default: () => 600}) private height!: number
  @Prop({default: () => 75}) private size!: number
  // @Prop() private height = 600  // generates error

  matrixSparse = generateData(nForTest)

  scale = scaleLinear()
    .domain([0, nForTest])
    .range([0, this.size * nForTest])

  // https://github.com/stared/quantum-game/blob/master/js/transition_heatmap.js
  colorComplex = (re: number, im: number) => {
    const angleInDegrees = (Math.atan2(im, re) * 360 / TAU + 360) % 360;
    const r = Math.sqrt(re * re + im * im)  // for pure color it should be always 1
    return hslToHex(angleInDegrees, 100, 100 - 50 * r)

  }

  tileMouseOver = (d) => console.log(d)

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

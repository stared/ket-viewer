<template>
  <div id="app">
    <select v-model="selected">
      <option v-for="option in options" v-bind:value="option" :key="`${option.name}`">
        {{ option.name }}
      </option>
    </select>
    <OperatorViewer
      :labelsIn="basis"
      :labelsOut="basis"
      :matrixElements="matrixElements"
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import OperatorViewer from './components/OperatorViewer.vue'
import * as qt from 'quantum-tensors'


@Component({
  components: {
    OperatorViewer
  }
})
export default class App extends Vue {
  options = [
    {name: "sugarSolution", operator: qt.sugarSolution()},
    {name: "sugarSolution x2", operator: qt.sugarSolution(1/4)},
    {name: "beamSplitter 0°", operator: qt.beamSplitter(0)},
    {name: "beamSplitter 45°", operator: qt.beamSplitter(45)},
    {name: "beamSplitter 90°", operator: qt.beamSplitter(90)},
    {name: "beamSplitter 135°", operator: qt.beamSplitter(135)},
    {name: "polarizingBeamsplitter 45°", operator: qt.polarizingBeamsplitter(45)},
    {name: "polarizingBeamsplitter 135", operator: qt.polarizingBeamsplitter(135)},
  ]
  selected = {name: "sugarSolution", operator: qt.sugarSolution()}

  // 
  // TODO: make in quantum-tensors
  get basis() { return ["⇢↔", "⇢↕", "⇡↔", "⇡↕", "⇠↔", "⇠↕", "⇣↔", "⇣↕"]}

  get matrixElements() {
    return this.selected.operator.entries.map((entry) => {
      return {
        i: 2 * entry.coordIn[0] + entry.coordIn[1],
        j: 2 * entry.coordOut[0] + entry.coordOut[1],
        re: entry.value.re,
        im: entry.value.im,
      }
    })
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

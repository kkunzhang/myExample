<template>
  <div class="container">
    <cy-anchor v-model="anchor">
      <cy-anchor-item label="滕王阁序(1)" name="1" class="anchor-item">
        <div class="text">
          {{ text }}
        </div>
      </cy-anchor-item>
      <cy-anchor-item label="滕王阁序(2)" name="2" class="anchor-item">
        <div class="text">
          {{ text }}
        </div>
      </cy-anchor-item>
      <cy-anchor-item label="滕王阁序(3)" name="3" class="anchor-item">
        <div class="text">
          {{ text }}
        </div>
      </cy-anchor-item>
      <cy-anchor-item label="滕王阁序(4)" name="4" class="anchor-item">
        <div class="text">
          {{ text }}
        </div>
      </cy-anchor-item>
    </cy-anchor>
  </div>
</template>

<script>
export default {
  name: 'Anchor',
  data() {
    return {
      anchor: '',
      text: `豫章故郡，饯。

时维九月，天人之旧馆。层峦耸翠，上出重霄；飞阁流丹，下临无地。鹤汀凫渚，穷岛屿之萦回；桂殿兰宫，即冈峦之体势。

披绣闼，俯雕甍，山原旷其盈视，。`
    }
  },
  watch: {
    // 监听楼层变化，当为父组件传递，或者按钮直达时，手动滚动到当前楼层
    anchor(newName, oldName) {
      console.log(newName)
    }
  }
}
</script>

<style scoped lang="scss">
.anchor-item {
  margin-bottom: 15px;
  background-color: #fff;

  .text {
    padding: 20px;
    white-space: pre-line;
  }
}
</style>

<template>
  <div class="py-3">
    <b-form-group
      label="起点："
      label-for="sid"
    >
      <b-form-input
        id="sid"
        v-model.number="sid"
        type="number"
      >
      </b-form-input>
    </b-form-group>

    <b-form-group
      label="终点："
      label-for="tid"
    >
      <b-form-input
        id="tid"
        v-model.number="tid"
        type="number"
      >
      </b-form-input>
    </b-form-group>
    <b-form-group>
      <b-button block variant="primary" @click="$emit('shortest-path', sid, tid)">
        最短路
      </b-button>
    </b-form-group>
    <hr>
    <b-form-group>
      <b-button block variant="success" @click="$emit('spanning-tree')">
        最小生成树（森林）
      </b-button>
    </b-form-group>
    <hr>
    <div>
      {{ message }}
    </div>
  </div>
</template>

<script>
  export default {
    name: "ControlPanel",
    props: {
      message: String
    },
    data() {
      return {
        sid: 18,
        tid: 114,
      }
    },
  }
</script>

<style scoped>

</style>
<template>
  <div class="parent" v-if="tree.children">
    <!--<div class="one" v-if="tree.level === 0 && tree.name">
      {{tree.name}}
    </div>-->
    <div class="child"
         :class="getClass(item, index)" v-bind:key="(item, index)" v-for="(item, index) in tree.children">
      <div class="child-title">

        <div class="child-title-name">
          {{item.name}}
        </div>
        <button class="child-title-btn" v-if="item.name" @click="pushHandle(item.name, tree)">晋级</button>
      </div>
      <tree v-if="item.children.length > 0" :tree="item" />
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: "tree",
  props: {
    tree: Object
  },
  data() {
    return {

    }
  },
  methods: {
    getClass(item, index) {
      return [
        `level-${item.level}`,
        item.level === 1 ? (index%2 === 0 ? 'left': 'right') :'',
        {'over': item.children.length === 0}
      ]
    },
    pushHandle(name, tree) {
      tree.name = name
      console.log(tree)
    }
  }
}
</script>

<style lang="scss">
  .one {
    position: absolute;
    top: 120px;
    width: 120px;
    height: 40px;
    box-sizing: border-box;
    border: 5px dashed #ffeb00;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .parent {
    position: relative;
    display: flex;
    justify-content: space-between;
    align-items: center;
    .child {
      flex: 1;
      text-align: center;
      margin: 5px;
      //border: 1px solid #42b983;
      display: flex;
      justify-content: flex-start;
      align-items: center;
    }
    .level-1 {
      display: flex;
      .parent {
        flex-flow: column;
      }
    }
    .left {
      flex-flow: row-reverse;
      .child {
        flex-flow: row-reverse;
      }
      .child-title {
        .child-title-btn {

        }
      }
    }
    .right {
      .child-title {
        flex-flow: row-reverse;
      }
    }
    .child-title {
      width: 150px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      border: 1px solid #42b983;
      box-sizing: border-box;
      padding: 0 5px;
      font-size: 13px;
      .child-title-name {

      }
      .child-title-btn {
        font-size: 12px;
      }
    }
  }

</style>

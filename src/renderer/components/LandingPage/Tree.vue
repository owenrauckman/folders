<template>
  <li>
    <div style="background: red;" ondrop="drop($event)">
      <span @click="toggle" v-if="isFolder">[{{ open ? '-' : '+' }}]</span>
      <input v-model="model.name" :class="{bold: isFolder}" draggable="true" @drop="onDrop($event)" @dragstart="dragstart($event)" @dragend="dragend($event)">
      <span @click="changeType">folderize</span>
    </div>
    <ul v-show="open" v-if="isFolder">
      <tree
        class="item"
        v-for="(model, index) in model.files"
        :key="index"
        :model="model">
      </tree>
      <li class="add" @click="addChild">+</li>
    </ul>
  </li>
</template>

<p>(You can double click on an item to turn it into a folder.)</p>

<script>
  export default {
    name: 'tree',
    props: {
      model: Object,
    },
    data() {
      return {
        open: false,
        fromComp: '', // DATA DRAGGING FROM (mb add key for folder vs file here)
      };
    },
    computed: {
      isFolder() {
        return this.model.files &&
       this.model.files.length;
      },
    },
    methods: {

      // SOME DROP
      dragstart(e) {
        console.log('starting drag');
        this.fromComp = this;
        e.dataTransfer.setData('data', 'data');
        e.dataTransfer.effectAllowed = 'move';
        return true;
      },
      dragend(e) {
        console.log('drag ENDED');
        console.log(e);
      },
      onDrop(e) {
        alert('sdf');
        console.log('DROPPED');
        console.log(e);
        this.fromComp.model.moveInto(this.model);
        this.isDragEnterNode = false;
      },

      // END SOME DROP
      toggle() {
        console.log('sdf');
        if (this.isFolder) {
          this.open = !this.open;
        }
      },
      changeType() {
        if (!this.isFolder) {
          this.$set(this.model, 'files', []);
          this.addChild();
          this.open = true;
        }
      },
      addChild() {
        this.model.files.push({
          name: 'new stuff',
        });
      },
    },
  };
</script>
<style>
.item {
  cursor: pointer;
}
.bold {
  font-weight: bold;
  background: lightgrey;
}
ul {
  padding-left: 1em;
  line-height: 1.5em;
  list-style-type: dot;
}
</style>

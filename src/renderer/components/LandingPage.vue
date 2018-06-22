<template>
  <div class="app__dashboard">
    <!-- <button class="alt" @click="importConfig">Import</button>
    <button class="alt" @click="createProject">Create</button> -->
    <aside class="app__dashboard__sidebar"></aside>
    <main class="app__dashboard__finder">
      <tree-node :data="treeData" />
      <div v-show="showSuccess">Project Successfully Created</div>
    </main>
  </div>
</template>

<script>
  import fs from 'fs';
  // import path from 'path';
  import TreeNode from './LandingPage/nested-list';


  export default {
    name: 'landing-page',
    components: { TreeNode },
    data() {
      return {

        // ACTUAL
        treeData: {
          label: 'folder1',
          selected: false,
          children: [
            {
              label: 'folder2',
              selected: false,
              children: [
                {
                  label: 'file1',
                },
                {
                  label: 'file2',
                  selected: false,
                  children: [
                    {
                      label: 'file1',
                    },
                    {
                      label: 'file2',
                    },
                  ],
                },
              ],
            },
          ],
        },
        showSuccess: false,
        selectedDir: '',
        projectName: '',
      };
    },
    methods: {
      open(link) {
        this.$electron.shell.openExternal(link);
      },

      /* imports config */
      importConfig() {
        this.showSuccess = false; // todo better implementation

        const configFile = this.$electron.remote.dialog.showOpenDialog({ properties: ['openFile'] });
        this.defaultFolder = JSON.parse(fs.readFileSync(configFile[0], 'utf8'));
        this.projectName = this.defaultFolder.name;
      },
      /* creates project */
      createProject() {
        this.selectedDir = this.$electron.remote.dialog.showOpenDialog({ properties: ['openDirectory'] });
        this.buildRecursiveFolders(this.selectedDir, this.treeData);
      },

      buildRecursiveFolders(directory, treeData) {
        if (treeData.children) {
          directory = `${directory}/${treeData.label}`;
          // console.log(`FOLD: ${directory}`);
          // CREATE DIRECTORIES
          if (!fs.existsSync(directory)) {
            fs.mkdirSync(directory);
          }
          // IF THIS IS TRUE THEN IT IS A FOLDER, call REC
          // ITS IN A LOOP CAUSE FILES IS AN ARRAY
          treeData.children.forEach(folder => this.buildRecursiveFolders(directory, folder));
        } else {
          // console.log(`-- ${treeData.name}`);
          // this used to have ,data, before utf8
          fs.writeFile(`${directory}/${treeData.label}`, 'utf8', (err) => {
            if (err) {
              console.log(err);
            }
            // todo: wrap in promise, handle errors
            // if (err) reject(err);
            // else resolve(data);
          });
        }
        // OTHERWISE
        return 'done';
      },

    },
  };
</script>

<style lang="scss">
.app{
  &__dashboard{
    padding: 2rem;
    display: flex;
    &__sidebar{
      flex: 1;
      max-width: 200px;
      // background: red;
    }
    &__finder{
      flex: 1;
    }
  }
}
</style>

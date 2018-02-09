<template>
  <div id="wrapper">
    <main>

      <div class="doc">

        <button class="alt" @click="importConfig">Import</button>
        <button class="alt" @click="createProject">Create</button>

        <!-- the demo root element -->
        <ul id="demo">
          <tree
            class="item"
            :model="defaultFolder">
          </tree>
        </ul>


      </div>

      <div v-show="showSuccess">Project Successfully Created</div>

      <div class="right-side">

      </div>
    </main>
  </div>
</template>

<script>
  import fs from 'fs';
  // import path from 'path';
  import SystemInformation from './LandingPage/SystemInformation';
  import Tree from './LandingPage/Tree';


  export default {
    name: 'landing-page',
    components: { SystemInformation, Tree },
    data() {
      return {
        treeData: {
          name: 'Project Name',
        },
        showSuccess: false,
        selectedDir: '',
        projectName: '',
        // THIS WAS TEST DATA
        defaultFolder: {
          name: 'Project',
          files: [
            // FOLDER 1
            {
              name: 'Folder1',
              files: [
                {
                  name: 'folder1Nest',
                },
              ],
            },
            // FOLDER 2
            {
              name: 'Folder2',
              files: [
                // NESTED FOLDER 2
                {
                  name: 'folder2Nest',
                  files: [
                    {
                      name: 'theDeepestFolder',
                    },
                  ],
                },
              ],
            },
          ],
        },
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

        // const dirToMake = `${this.selectedDir}/${this.treeData.name}`;
        // this.recursivelyWriteFiles(dirToMake, this.treeData);
        // console.log(this.iterate(this.treeData, 0, this.selectedDir));
        // this.buildRecursiveFolders(this.treeData);
        this.buildRecursiveFolders(this.selectedDir, this.treeData);
      },

      buildRecursiveFolders(directory, treeData) {
        if (treeData.files) {
          directory = `${directory}/${treeData.name}`;
          console.log(`FOLD: ${directory}`);
          // CREATE DIRECTORIES
          if (!fs.existsSync(directory)) {
            fs.mkdirSync(directory);
          }
          // IF THIS IS TRUE THEN IT IS A FOLDER, call REC
          // ITS IN A LOOP CAUSE FILES IS AN ARRAY
          treeData.files.forEach(folder => this.buildRecursiveFolders(directory, folder));
        } else {
          console.log(`-- ${treeData.name}`);
          // this used to have ,data, before utf8
          fs.writeFile(`${directory}/${treeData.name}`, 'utf8', (err) => {
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

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  #wrapper {
    background:
      radial-gradient(
        ellipse at top left,
        rgba(255, 255, 255, 1) 40%,
        rgba(229, 229, 229, .9) 100%
      );
    height: 100vh;
    padding: 60px 80px;
    width: 100vw;
  }

  #logo {
    height: auto;
    margin-bottom: 20px;
    width: 420px;
  }

  .in{
    display: block;
    margin: 5rem 0 1rem 0;
  }

  main {
    display: flex;
    justify-content: space-between;
    flex-direction: column;
  }

  main > div { flex-basis: 50%; }

  .left-side {
    display: flex;
    flex-direction: column;
  }

  .welcome {
    color: #555;
    font-size: 23px;
    margin-bottom: 10px;
  }

  .title {
    color: #2c3e50;
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 6px;
  }

  .title.alt {
    font-size: 18px;
    margin-bottom: 10px;
  }

  .doc p {
    color: black;
    margin-bottom: 10px;
  }

  .doc button {
    font-size: .8em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 2em;
    border-radius: 2em;
    display: inline-block;
    color: #fff;
    background-color: #4fc08d;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #4fc08d;
  }

  .doc button.alt {
    color: #42b983;
    background-color: transparent;
  }
</style>

<script lang="ts">
import { defineComponent } from 'vue'
import * as monaco from 'monaco-editor'
import Header from './components/Header.vue';

export default defineComponent({
  data() {
    return {
      originalVal: 'heLLo world!',
      modifiedVal: 'hello orlando!',
      originalInput: '// Original Entry' as string,
      modifiedInput: '// Modified Entry' as string,
      test: '' as any,
    }
  },
  components: {
    Header,
  },
  mounted() {
    this.initializeInput();
  },
  methods: {
    initializeInput() {
    this.$nextTick(() => {
      const originalInput = monaco.editor.create(this.$refs.originalInput as HTMLDivElement, {
        value: this.originalInput,
      });
      originalInput.onDidChangeModelContent((event) => {
        this.originalInput = originalInput.getValue();
      });
      this.test = originalInput;
      const modifiedInput = monaco.editor.create(this.$refs.modifiedInput as HTMLDivElement, {
        value: this.modifiedInput,
      });
      modifiedInput.onDidChangeModelContent((event) => {
        this.modifiedInput = modifiedInput.getValue();
      });
    });
    },
    createDifference() {
      const originalModel = monaco.editor.createModel(this.originalInput, 'text/plain');
      const modifiedModel = monaco.editor.createModel(this.modifiedInput, 'text/plain');
      this.$nextTick(() => {
        let diffEl = this.$refs.monaco as HTMLDivElement;
        if (diffEl) {
          diffEl.innerHTML = '';
        }
        const diffEditor = monaco.editor.createDiffEditor(diffEl);
        diffEditor.setModel({
          original: originalModel,
          modified: modifiedModel,
        })
      });

    },
  }
})
</script>

<template>
  <div>
    <Header/>
    <div class="main">
      <div class="userInput">
      <div ref="originalInput" id="originalInput"></div>
      <div ref="modifiedInput" id="modifiedInput"></div>
      <div class="actions">
        <button @click="createDifference" class="difference">
          Check Difference
        </button>
      </div>
    </div>
    <div ref="monaco" id="monaco">
    </div>
    </div>

  </div>
</template>

<style scoped>

.main {
  display: flex;
}

#monaco {
  height: calc(100vh - 100px);
  width: 100%;
}

.userInput {
    display: flex;
    flex-direction: column;
    width: 45%;
}

#originalInput,
#modifiedInput {
  height: 42vh;
  width: 100%;
}

.difference {
  border: none;
  background-color: #3d5a80 ;
  width: 100%;
  font-size: 16px;
  height: 50px;
  color: white;
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  cursor: pointer;
}

.difference:hover {
  background-color: #293241;
  color: #e0fbfc;
  transition: all 100ms linear;
}

</style>

<template>
  <div class="flex min-h-screen w-full bg-slate-950 p-2">
    <div
      class="mx-2 w-full border p-2"
      :class="!idToModify && 'cursor-pointer shadow-slate-200'"
      @click="idToModify = null"
    >
      <TreeView
        :handle-click="getSelectedTarget"
        :current-view="treeData"
        :id-to-modify="idToModify"
        :to-remove="toRemove"
      />
    </div>
    <div class="w-full">
      <div v-if="!addOrEditFileOrFolder">
        <SButton
          v-if="selectedData.type === 'folder' || !idToModify"
          @click="handleActionType('folder')"
        >
          add folder
        </SButton>
        <SButton
          v-if="selectedData.type === 'folder' || !idToModify"
          @click="handleActionType('file')"
        >
          add file
        </SButton>
        <SButton
          v-if="treeData.length"
          class="px-2"
          @click="handleActionType('edit')"
        >
          edit
        </SButton>
        <SButton
          v-if="treeData.length"
          @click="handleActionType('remove')"
        >
          remove
        </SButton>
      </div>
      <div
        v-if="addOrEditFileOrFolder"
        class="flex"
      >
        <input
          v-if="type !== 'remove'"
          v-model="nodeName"
          class="peer h-10 w-full rounded-md border border-white bg-gray-950 px-4 drop-shadow-sm transition-all duration-200 ease-in-out focus:bg-gray-950 focus:ring-2 focus:ring-blue-400"
          type="text"
          name="addOrEditFileOrFolder"
          placeholder="add your name"
        >
        <SButton
          class="mx-3"
          @click="handleActionBaseOnType"
        >
          ok
        </SButton>
        <SButton @click="addOrEditFileOrFolder = false">
          cancel
        </SButton>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import TreeView from "@/components/TreeView.vue";
import SButton from "@/components/SButton.vue";

const treeData = ref([]);
const selectedData = ref({});
const idToModify = ref(null);
const addOrEditFileOrFolder = ref(false);
const type = ref("");
const nodeName = ref("");
const toRemove = ref(false);

function getSelectedTarget(currentView) {
  selectedData.value = currentView;
  idToModify.value = selectedData.value.id;
}
function handleActionBaseOnType() {
  if (type.value === "folder" || type.value === "file") {
    addFolderOrFile();
  } else if (type.value === "edit") {
    editView();
  } else {
    removeView();
  }
  setTimeout(() => {
    toRemove.value = false;
  }, 1);
  nodeName.value = "";
  addOrEditFileOrFolder.value = false;
}

function newNode() {
  return {
    title: nodeName.value,
    type: type.value,
    id: window.crypto.randomUUID(),
    ...(type.value === "folder" && !selectedData.value.child && { child: [] }),
  };
}
function handleActionType(actionType) {
  addOrEditFileOrFolder.value = true;
  type.value = actionType;
}
function addFolderOrFile() {
  if (!idToModify.value) {
    treeData.value.push(newNode());
  }
  if (!selectedData.value.child) {
    selectedData.value.child = [];
  }
  selectedData.value.child.push(newNode());
}
function editView() {
  Object.assign(selectedData.value, {
    ...selectedData.value,
    title: nodeName.value,
  });
}
function removeView() {
  toRemove.value = true;
  if (!treeData.value.length) {
    idToModify.value = null;
  }
}
</script>

<template>
  <div
    v-for="(view, index) in convertedCurrentView"
    :key="view.id"
    class="w-full cursor-pointer"
    @click.stop="handleClick(view, index)"
  >
    <template v-if="view.type === 'folder'">
      <button
        class="text-left"
        :class="
          idToModify === view.id && 'bg-slate-600  rounded px-2 block w-full'
        "
        :style="`padding-left:${leftPadding}px`"
        @click.stop="
          handleExpand(view);
          handleClick(view, index);
        "
      >
        {{ view.isExpand ? "-" : "+" }}
        <span>
          {{ view.title }}
        </span>
      </button>

      <TreeView
        v-if="view.isExpand"
        :current-view="view.child"
        :handle-click="handleClick"
        :id-to-modify="idToModify"
        :to-remove="toRemove"
        :left-padding="leftPadding + 40"
      />
    </template>
    <template v-else>
      <div
        :class="
          idToModify === view.id && ' bg-slate-600  rounded px-2 block w-full'
        "
        :style="`padding-left:${leftPadding}px`"
      >
        {{ view.title }}
      </div>
    </template>
  </div>
</template>

<script setup>
import { toRef, watch } from "vue";
const props = defineProps({
  toRemove: Boolean,
  currentView: {
    type: Array,
    default: () => [],
  },
  leftPadding: {
    type: Number,
    default: 0,
  },
  handleClick: {
    type: Function,
    default: () => ({}),
  },
  idToModify: { type: [String, Number], default: 0 },
});

const convertedCurrentView = toRef(props, "currentView");
function handleExpand(view) {
  view.isExpand = !view.isExpand;
}
function findIdBasedOdIdToModify(idToModify) {
  return convertedCurrentView.value.findIndex(({ id }) => id === idToModify);
}
watch(
  () => props.toRemove,
  (val) => {
    if (val) {
      const indexOfModifiedId = findIdBasedOdIdToModify(props.idToModify);
      if (indexOfModifiedId !== -1) {
        convertedCurrentView.value.splice(indexOfModifiedId, 1);
      }
    }
  }
);
</script>

<template>
    <div class="text-gray-600">{{ labelDisplay }}

        <el-tooltip v-if="props.help" class="item" effect="dark" :content="props.help" placement="top-start">
      <el-button size="mini" class="ml-1 shadow-md" circle plain><i class="fas fa-question fa-fw"></i></el-button>
    </el-tooltip>
    </div>
    
</template>

<script setup>
import { startCase } from "lodash";
import { computed } from "vue";

const props = defineProps({
    label: { type: String, required: true }, help: { type: String, required: false },
});
const labelDisplay = computed(() => {
    return startCase(props.label);
});
</script>

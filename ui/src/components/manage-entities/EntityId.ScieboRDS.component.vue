<template>
    <div class="w-3/4 xl:w-8/12 flex flex-col  px-3">
        <div class="flex flex-row">
            <el-input
                class="w-full style-element"
                @input="debouncedSave"
                v-model="internalValue"
            ></el-input>
        </div>
    </div>
</template>

<script setup>
import { debounce } from "lodash";
import { ref } from "vue";

const emit = defineEmits(["save:property"]);
const props = defineProps({
    value: {
        type: String,
    },
});

const internalValue = ref(props.value);
const debouncedSave = debounce(save, 1000);
function save() {
    emit("save:property", {
        property: "eid",
        value: internalValue.value,
    });
}
</script>

<style lang="scss" scoped>
.style-element {
    min-width: 400px;
}
</style>

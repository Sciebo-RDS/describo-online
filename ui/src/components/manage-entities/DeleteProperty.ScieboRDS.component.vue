<template>
    <div class="">
        <el-button @click="deleteProperty" class="!border-0 !bg-transparent !h-auto">
            <div v-show="props.type === 'unlink'" class="inline-block">
                <i class="fas fa-close"></i>
            </div>
            <div v-show="props.type === 'delete'" class="inline-block">
                <i class="fas fa-trash"></i>
            </div>
        </el-button>
    </div>
</template>

<style scoped>
.el-button:hover {
    color: red;
}
</style>

<script setup>
const emit = defineEmits("delete:property");
const props = defineProps({
    type: {
        type: String,
        default: "unlink",
        validator: (val) => {
            return ["delete", "unlink"].includes(val);
        },
    },
    property: {
        type: Object,
        required: true,
    },
});
async function deleteProperty() {
    emit("delete:property", {
        entityId: props.property.entityId,
        propertyId: props.property.id,
    });
}
</script>

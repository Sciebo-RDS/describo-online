<template>
    <div class="flex flex-row p-1">
        <div class="w-1/4 xl:w-2/12 pt-2 text-gray-600">Type</div>
        <div class="w-3/4 xl:w-8/12 flex flex-col  px-3">{{ entity.etype }}</div>
    </div>
</template>

<script setup>
const props = defineProps({
    entity: {
        type: Object,
        required: true,
    },
});
</script>

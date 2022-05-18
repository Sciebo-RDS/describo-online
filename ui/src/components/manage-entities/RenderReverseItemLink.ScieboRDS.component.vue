<template>
    <div
        class="flex flex-col bg-yellow-200 p-1 cursor-pointer border border-solid border-slate-500"
        @click="loadEntity"
    >
        <div class="text-sm">
            <span class="text-gray-800 mr-2 flex flex-row">
                <type-icon-component
                    class="mr-2 text-gray-700"
                    :type="entity.tgtEntityType"
                    v-if="entity.tgtEntityType"
                />
                {{ entity.tgtEntityType }}
                - {{ entity.tgtEntityName }}:
                {{ entity.name }}
            </span>
        </div>
    </div>
</template>

<script>
import TypeIconComponent from "./TypeIcon.component.vue";

export default {
    components: {
        TypeIconComponent,
    },
    props: {
        entity: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {};
    },
    methods: {
        loadEntity() {
            this.$store.commit("setSelectedEntity", {
                id: this.entity.tgtEntityId,
            });
        },
    },
};
</script>

<template>
    <div class="flex flex-row">
        <div class="flex flex-col bg-indigo-200 p-1 cursor-pointer border-y border-l border-solid border-slate-500 px-2.5" @click="loadEntity">
            <div class="text-sm prose-sm flex flex-row space-x-1">
                <type-icon-component
                    class="mr-2 text-gray-700"
                    :type="entity.tgtEntityType"
                    v-if="entity.tgtEntityType"
                />
                <div>{{ entity.tgtEntityType }}:</div>
                <span v-if="entity.tgtEntityName">{{ entity.tgtEntityName }}</span>
                <span v-else>{{ entity.tgtEntityEid }}</span>
            </div>
        </div>
        <delete-property-component
            class="bg-indigo-200 cursor-pointer border border-solid border-slate-500"
            :property="entity"
            @delete:property="deleteProperty"
        />
    </div>
</template>

<script>
import TypeIconComponent from "./TypeIcon.component.vue";
import DeletePropertyComponent from "./DeleteProperty.ScieboRDS.component.vue";

export default {
    components: {
        TypeIconComponent,
        DeletePropertyComponent,
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
        deleteProperty(data) {
            this.$emit("delete:property", data);
        },
    },
};
</script>

<template>
    <div class="flex flex-col space-y-1 p-1">
        <!-- entity id -->
        <div
            class="flex flex-row"
            :class="{
                'bg-green-200 p-1 rounded': update.success === 'eid',
                'bg-red-200 p-1 rounded': update.error === 'eid',
            }"
        >
            <div class="w-1/4 xl:w-2/12 pt-2 text-gray-600">Identifier</div>
            <entity-id-component
                :value.sync="entity.eid"
                @save:property="saveEntityProperty"
                v-if="!['Dataset', 'File'].includes(entity.etype)"
            />
            <div v-if="['Dataset', 'File'].includes(entity.etype)" class="w-3/4 xl:w-8/12 flex flex-col  px-3">
                {{ entity.eid }}
            </div>
        </div>
    </div>
</template>

<script>
import EntityIdComponent from "./EntityId.ScieboRDS.component.vue";
import TextComponent from "./Text.ScieboRDS.component.vue";

export default {
    components: {
        EntityIdComponent,
        TextComponent,
    },
    props: {
        entity: {
            type: Object,
            required: true,
        },
        dataService: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            update: {
                error: false,
                success: false,
            },
        };
    },
    methods: {
        async saveEntityProperty(data) {
            try {
                await this.dataService.updateEntityProperty({
                    id: this.entity.id,
                    property: data.property,
                    value: data.value,
                });
                this.update.success = data.property;
                setTimeout(() => {
                    this.update.success = false;
                }, 1500);
            } catch (error) {
                this.update.error = data.property;
                setTimeout(() => {
                    this.update.error = false;
                    this.$emit("getEntity");
                }, 1500);
            }
        },
    },
};
</script>

<template>
    <div class="shadow-md" v-if="visible">
        <div class="flex flex-row">
            <div class="text-sm flex-grow pt-4">Save this crate as a template for re-use

                <div class="flex flex-row space-x-2 flex-grow mt-3" v-if="entityCount < maxEntitiesPerTemplate">
            <div class="flex-grow">
                <el-input
                    class="w-full"
                    v-model="crateName"
                    size="small"
                    placeholder="Provide a name for the crate template"
                    @keydown.enter="save"
                />
            </div>
            <div>
                <el-button @click="save" size="small" :disabled="!crateName">
                    <div class="mr-1">
                        <i class="fas fa-save"></i>
                    </div>
                    Save
                </el-button>
            </div>
        </div>
            </div>
            <div class="flex flex-flow p-2">
                <el-button @click="close" type="danger" size="small" plain>
                    <i class="fas fa-xmark"></i>
                </el-button>
            </div>
        </div>
        <div class="pt-3">
        
        
        </div>
    </div>
</template>

<script>
export default {
    props: {
        entity: {
            type: Object,
            required: true,
        },
        visible: {
            type: Boolean,
            required: true,
        },
    },
    data() {
        return {
            entityCount: 0,
            maxEntitiesPerTemplate: this.$store.state.configuration.maxEntitiesPerTemplate,
            crateName: undefined,
        };
    },
    methods: {
        close() {
            this.$emit("close");
            this.crateName = '';
        },
        save() {
            this.$emit("save:crate-as-template", { name: this.crateName });
            this.crateName = '';
        },
    },
};
</script>

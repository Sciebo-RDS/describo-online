<template>
    <div v-if="visible">
    <div class="flex flex-grow w-full">
        <div v-if="!selectedProperty" class="flex flex-col m-2 w-full">
            <div>
                <el-input
                    v-model="filter"
                    size="small"
                    placeholder="Filter For Attributes"
                    clearable
                ></el-input>
            </div>
            <div class="flex flex-row space-x-2">
                <!-- <div class="w-48 text-sm pt-1">Add a property to this entity</div> -->
                <div class="flex-grow">
                    <div class="h-48 overflow-scroll overflow-x-hidden border p-4 bg-white flex flex-col space-y-1">
                        <div
                            v-for="(item, idx) in properties"
                            :key="idx"
                            class="cursor-pointer p-1 hover:bg-gray-100"
                            @click="handlePropertySelection(item)"
                        >
                            <div class="flex flex-row">
                                <div class="w-64 text-gray-600" v-if="item.label">
                                    {{ item.label }}
                                </div>
                                <div class="w-64 text-gray-600" v-else>
                                    {{ item.name }}
                                </div>
                                <div class="text-gray-500 w-full">
                                    {{ item.help }}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <div v-if="selectedProperty" class="flex flex-col flex-grow m-2 mt-3">
            <div class="text-gray-600 mb-2">
                {{ selectedProperty.label }}
            </div>
            <div class="text-xs text-gray-600">
                {{ selectedProperty.help }}
            </div>
            <add-component
                class="mt-4"
                :property="selectedProperty.name"
                :definition="selectedProperty"
                :embedded="true"
                @create:property="createProperty"
                @create:entity="createAndLinkEntity"
                @link:entity="linkEntity"
                @add:template="addTemplate"
            />
        </div>
        <div class="mt-2">
                <el-button @click="close" type="danger" size="small" class="shadow-md" plain><i class="fas fa-times"></i></el-button>
        </div>
    </div>
    </div>
</template>

<script>
import AddComponent from "./Add.ScieboRDS.component.vue";

export default {
    components: {
        AddComponent,
    },
    props: {
        inputs: {
            type: Array,
            required: true,
        },
        visible: {
            type: Boolean,
            required: true,
        },
    },
    data() {
        return {
            selectedProperty: undefined,
            addType: undefined,
            filter: undefined,
        };
    },
    computed: {
        properties: function () {
            if (!this.filter) return this.inputs;
            return this.inputs.filter((i) => {
                let re = new RegExp(this.filter, "i");
                return i.name.match(re);
            });
        },
    },
    methods: {
        close() {
            this.selectedProperty = undefined;
            this.addType = undefined;
            this.$emit("close");
        },
        handlePropertySelection(item) {
            this.selectedProperty = item;
        },
        add({ type }) {
            this.addType = type;
        },
        createProperty(data) {
            this.$emit("create:property", data);
            this.close();
        },
        createAndLinkEntity(data) {
            this.$emit("create-and-link:entity", data);
            this.close();
        },
        linkEntity(data) {
            this.$emit("link:entity", data);
            this.close();
        },
        addTemplate(data) {
            this.$emit("add:template", data);
            this.close();
        },
    },
};
</script>

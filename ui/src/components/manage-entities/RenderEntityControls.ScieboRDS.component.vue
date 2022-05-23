<template>
    <div>
        <add-property-dialog
            class="p-2 m-2 border-t border-slate-300"
            :visible="addPropertyDialogVisible"
            :inputs="inputsWithNameFilteredOut()"
            @close="addPropertyDialogVisible = false"
            @create:property="createProperty"
            @create-and-link:entity="createAndLinkEntity"
            @link:entity="linkEntity"
            @add:template="addTemplateAndLinkEntity"
        />
        <save-crate-as-template-dialog
            class="border-2 border-indigo-200 pl-6 pb-4 m-2 rounded"
            :visible="saveCrateAsTemplateDialogVisible"
            :entity="entity"
            @close="toggleSaveCrateDialog"
            @save:crate-as-template="saveCrateAsTemplate"
        />
        <div class="flex flex-row space-x-2 mb-4 p-2">
            <!-- navbar : controls -->
            
            <div>
                <el-button
                    @click="loadRootDataset"
                    size="small"
                    v-if="!(entity && entity.eid === './')"
                    class="shadow-md"
                >
                <div class="mr-1">
                        <i class="fas fa-arrow-circle-left"></i>
                    </div>
                    Back To Root
                </el-button>
            </div>
            <div v-if="definition.classDefinitionType !== 'override' && !(addPropertyDialogVisible || saveCrateAsTemplateDialogVisible)">
                <el-button @click="toggleAddPropertyDialog" class="shadow-md" size="small" type="primary">
                    <div class="mr-1">
                        <i class="fas fa-code"></i>
                    </div>
                    Add Property
                </el-button>
            </div>
            <div class="flex flex-grow"></div>
            <div class="flex flex-row space-x-1">
                <div v-if="isRootDataset && !(addPropertyDialogVisible || saveCrateAsTemplateDialogVisible)">
                    <el-button
                        @click="toggleSaveCrateDialog"
                        type="info"
                        size="small"
                        class="shadow-md"
                        plain
                    >
                        <div class="inline-block">
                            <i class="fas fa-save"></i>
                        </div>
                        <div
                            class="inline-block ml-1 xl:inline-block xl:ml-1"
                            :class="{ hidden: entity.etype === 'File' }"
                        >
                            Save Crate as Template
                        </div>
                    </el-button>
                </div>
                <div v-if="!addPropertyDialogVisible">
                    <el-button
                        @click="saveEntityAsTemplate"
                        type="info"
                        size="small"
                        v-if="!isRootDataset"
                        class="shadow-md"
                        plain
                    >
                        <div class="inline-block">
                            <i class="fas fa-save"></i>
                        </div>
                        <div
                            class="inline-block ml-1 xl:inline-block xl:ml-1"
                            :class="{ hidden: entity.etype === 'File' }"
                        >
                            Save Entity Template
                        </div>
                    </el-button>
                </div>
                <div v-if="!addPropertyDialogVisible">
                    <el-button
                        @click="deleteEntity"
                        type="danger"
                        size="small"
                        v-if="!isRootDataset"
                        class="shadow-md"
                    >
                        <div class="inline-block">
                            <i class="fas fa-trash"></i>
                        </div>
                        <div
                            class="inline-block ml-1 xl:inline-block xl:ml-1"
                            :class="{ hidden: entity.etype === 'File' }"
                        >
                            Delete Entity
                        </div>
                    </el-button>
                </div>
            </div>
            <!-- /navbar: controls -->
        </div>
        
        
    </div>
</template>

<script>
import AddPropertyDialog from "./AddPropertyDialog.ScieboRDS.component.vue";
import SaveCrateAsTemplateDialog from "./SaveCrateAsTemplateDialog.ScieboRDS.component.vue";
import DataService from "./data.service.js";

export default {
    components: {
        AddPropertyDialog,
        SaveCrateAsTemplateDialog,
    },
    props: {
        entity: {
            type: Object,
            required: true,
        },
        definition: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            loading: false,
            dataService: undefined,
            error: undefined,
            addPropertyDialogVisible: false,
            saveCrateAsTemplateDialogVisible: false,
        };
    },
    computed: {
        isRootDataset: function() {
            return this.entity && this.entity.eid === "./";
        },
    },
    mounted() {
        this.dataService = new DataService();
    },
    methods: {
        loadRootDataset() {
            this.$store.commit("setSelectedEntity", { id: "RootDataset" });
        },
        async toggleAddPropertyDialog() {
            this.saveCrateAsTemplateDialogVisible = false;
            this.addPropertyDialogVisible = !this.addPropertyDialogVisible;
        },
        async toggleSaveCrateDialog() {
            this.addPropertyDialogVisible = false;
            this.saveCrateAsTemplateDialogVisible = !this.saveCrateAsTemplateDialogVisible;
        },
        inputsWithNameFilteredOut() {
            return this.definition.inputs.filter((i) => i.name !== "name");
        },
        async createProperty({ property, value }) {
            await this.dataService.createProperty({
                srcEntityId: this.entity.id,
                property,
                value,
            });
            this.$emit("refresh");
        },
        async createAndLinkEntity({ property, etype, entityName }) {
            let { entity } = await this.dataService.createEntity({
                name: entityName,
                etype,
            });
            await this.linkEntity({ property, tgtEntityId: entity.id });
        },
        async linkEntity({ property, tgtEntityId }) {
            await this.dataService.associate({
                srcEntityId: this.entity.id,
                property,
                tgtEntityId,
            });
            this.$emit("refresh");
        },
        async addTemplateAndLinkEntity({ property, templateId }) {
            let { entity } = await this.dataService.addTemplateToCrate({ templateId });
            await this.linkEntity({ property, tgtEntityId: entity.id });
        },
        async deleteEntity() {
            await this.dataService.deleteEntity({ id: this.entity.id });
            this.$store.commit("setSelectedEntity", { id: "RootDataset" });
        },
        async saveEntityAsTemplate() {
            await this.dataService.saveEntityAsTemplate({ id: this.entity.id });
        },
        async saveCrateAsTemplate({ name }) {
            await this.dataService.saveCrateAsTemplate({ name });
            this.toggleSaveCrateDialog();
        },
        resolveFilePath(id) {
            let filePath = `${this.$store.state.target.folder.path}/${id}`;
            return filePath;
        },
    },
};
</script>

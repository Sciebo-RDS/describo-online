<template>
    <div class="flex flex-row flex-grow p-1">
        <div class="w-1/4 xl:w-2/12 flex flex-col">
            <div>
                <display-property-name-component
                    :label="name"
                    :help="help"
                    class="inline-block"
                    :class="{ 'text-red-600': isRequired && !isValid }"
                />
                <el-badge is-dot class="animate-pulse -ml-1 -mt-2" v-if="isRequired && !isValid">
                </el-badge>
            </div>
            <!-- <div class="text-gray-500 text-xs pr-1">{{ help }}</div> -->
            <div v-if="!props.definition" class="text-red-600 text-xs">
                (not defined in profile)
            </div>
        </div>
        <div class="w-3/4 xl:w-8/12 flex flex-col px-2">
            <add-component
                class="mx-1"
                :property="props.name"
                :definition="props.definition"
                :embedded="false"
                @create:property="createProperty"
                @create:entity="createEntity"
                @create:object="createObject"
                @link:entity="linkEntity"
                @add:template="addTemplate"
                v-if="
                    (props.definition && props.definition.multiple) ||
                    (props.definition && !props.definition.multiple && !props.values.length)
                "
            />
            <div class="flex flex-row flex-wrap">
                <div v-for="instance of props.values" :key="instance.id" class="flex flex-row m-1">
                    <render-entity-property-instance-component
                        :property="instance"
                        :definition="props.definition"
                        @save:property="saveProperty"
                        @delete:property="deleteProperty"
                        @refresh="refresh"
                    />
                    <delete-property-component
                        class=""
                        type="delete"
                        :property="instance"
                        @delete:property="deleteProperty"
                        v-if="isNotValue && instance.value"
                    />
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import RenderEntityPropertyInstanceComponent from "./RenderEntityPropertyInstance.ScieboRDS.component.vue";
import DeletePropertyComponent from "./DeleteProperty.ScieboRDS.component.vue";
import InformationComponent from "../Information.component.vue";
import AddComponent from "./Add.ScieboRDS.component.vue";
import DisplayPropertyNameComponent from "./DisplayPropertyName.ScieboRDS.component.vue";
import { ref, computed } from "vue";

const props = defineProps({
    name: {
        type: String,
        required: true,
    },
    values: {
        type: Array,
        required: true,
    },
    definition: {
        type: Object,
    },
});
const emit = defineEmits([
    "refresh",
    "create:property",
    "create:entity",
    "create:object",
    "link:entity",
    "add:template",
    "save:property",
    "delete:property",
]);
const help = ref(props.definition?.help);
const showHelp = ref(false);

const isValid = computed(() => {
    return props.values.length ? true : false;
});
const isRequired = computed(() => {
    return props.definition?.required;
});
const isNotValue = computed(() => {
    return props.definition?.type !== "Value";
});

function refresh() {
    emit("refresh");
}
function createProperty(data) {
    emit("create:property", data);
}
function createEntity(data) {
    emit("create:entity", data);
}
function createObject(data) {
    emit("create:object", data);
}
function linkEntity(data) {
    emit("link:entity", data);
}
function addTemplate(data) {
    emit("add:template", data);
}
function saveProperty(data) {
    emit("save:property", data);
}
function deleteProperty(data) {
    emit("delete:property", data);
}
</script>

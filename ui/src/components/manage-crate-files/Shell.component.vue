<template>
    <div class="box-card">
        <div class="flex flex-col space-y-2">
            <div class="flex flex-col place-items-center mb-3">
            <information-component type="info" class="w-3/4">
                Use the controls below to add entries into the collection so you can annotate them.<br>
                You must expand each subfolder to load the child nodes. If you don't you'll
                        only get the folders.
            </information-component>
            </div>

            <file-browser-component
                :resource="resource"
                :root="folder"
                mode="openFile"
                :enable-file-selector="true"
                @selected-nodes="saveSelectedNodes"
            />
        </div>
    </div>
</template>

<script setup>
import FileBrowserComponent from "@/components/filebrowser/FileBrowser.component.vue";
import InformationComponent from "../Information.component.vue";
import { useStore } from "vuex";
const store = useStore();
import { inject, ref } from "vue";
const $http = inject("$http");

const resource = ref(store.state.target.resource);
const folder = ref(store.state.target.folder);

async function saveSelectedNodes(nodes) {
    try {
        await $http.post({ route: "/files", body: { files: nodes } });
    } catch (error) {
        console.log(error);
    }
}
</script>

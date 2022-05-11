<template>
    <div v-if="!profile.file" class="bg-white"> 
        <div style="display: flex; flex-direction: row; align-items: center; justify-content: center; padding-bottom: 50px;">
            <div class="my-4 text-gray-700" style="font-weight: bold; padding: 5px;">
                Choose an open access repository to publish your research data   
            </div>
            <el-tooltip content="Explanation for 'choose repository'" placement="top">
                    <el-button size="mini" type="primary" circle plain> 
                        <i class="fas fa-question fa-fw"></i>
                    </el-button>
            </el-tooltip>
        </div>
        <!-- add logo attribute to profiles -->
        <div style="display: flex; justify-content: center">
            <el-empty v-if="!data.profiles" description="No Repository Profiles Available. Please contact <email>."></el-empty>
            <div v-else style="display: flex; justify-content: space-between; width: 400px;"
            v-for="profile in data.profiles"
            :key="profile.file"
            :value="profile.file">
                <button @click="data.pendingProfile = profile.file" style="outline-color: transparent" >        
                    <img src="https://upload.wikimedia.org/wikipedia/commons/d/dc/Grumpy_Cat_%2814556024763%29_%28cropped%29.jpg"  :class="!(data.pendingProfile == profile.name) ? 'opacity-30' : ''"  :alt="profile.name" :id="profile.name">
                    {{ profile.name }}
                </button>
            </div>
        </div>
        <!-- Button will be replaced with Sciebo RDS "next Step" button -->
        <div>
                <el-button
                    type="primary"
                    size="small"
                    @click="useProfile"
                    :disabled="!data.pendingProfile"
                >
                    Use repository
                </el-button>
            </div>
    </div>
</template>

<style scoped>
.opaque {
    opacity: 0.3;
}
</style>

<script setup>
import { restoreSessionProfile, getProfiles, setProfile } from "./session-handlers";

import { onMounted, computed, reactive, } from "vue";
import { useStore } from "vuex";
const store = useStore();

const data = reactive({
    profiles: [],
    pendingProfile: undefined,
    selectedProfile: undefined,
});
const profile = computed(() => {
    return store.state.profile;
});
onMounted(() => {
    init();
});

async function init() {
    await restoreSessionProfile();
    data.profiles = await getProfiles();
}
async function useProfile() {
    const profile = data.profiles.filter((p) => p.file === data.pendingProfile)[0];
    await setProfile({ profile });
}
async function selectNewProfile() {
    await setProfile({ profile: {} });
}

</script>

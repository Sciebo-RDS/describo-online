<template>
    <div v-if="!profile.file" class="bg-white"> 
        <div class="flex justify-center pb-5  p-5">
            <div class="my-2 mx-2 text-gray-700 font-bold">
                Choose an open access repository to publish your research data   
            </div>
            <el-tooltip content="Explanation for 'choose repository'" placement="top">
                    <el-button size="mini" type="primary"  circle plain> 
                        <i class="fas fa-question fa-fw"></i>
                    </el-button>
            </el-tooltip>
        </div>
        <!-- add logo attribute to profiles -->
        <div class="flex justify-center">
            <el-empty v-if="!data.profiles" description="No Repository Profiles Available. Please contact <email>."></el-empty>
            <div v-else style="width: 400px;"
            v-for="profile in data.profiles"
            :key="profile.file"
            :value="profile.file"
            class="flex justify-between">
                <button @click="data.pendingProfile = profile.file">        
                    <el-image :src="profile.logo" :class="!(data.pendingProfile == profile.name) ? 'opacity-30' : ''" style="width:150px; height: 100px" fit="contain" :alt="profile.name" :id="profile.name" />
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

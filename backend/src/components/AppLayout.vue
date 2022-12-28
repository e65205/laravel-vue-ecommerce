<template>
  <div v-if="currentUser.id" class="min-h-full bg-gray-200 flex">
    <!--    Sidebar-->
    <Sidebar :class="{ '-ml-[160px]': !sidebarOpened }" />
    <!--/    Sidebar-->

    <div class="flex-1">
      <Navbar @toggle-sidebar="toggleSidebar"></Navbar>
      <!--      Content-->
      <main class="p-6">
        <router-view></router-view>
      </main>
      <!--      Content-->
    </div>
  </div>
  <div v-else class="min-h-full bg=gray-400 flex items-center justify-center">
   <spinner v-if="showSpinner" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";
import Sidebar from "./Sidebar.vue";
import Navbar from "./Navbar.vue";
import store from "../store";
import Spinner from "./core/Spinner.vue";

const {text} = defineProps({
  title: String,
  text: {
    type: String,
    default: 'Please Wait...'
  },
  class: {
    type: String,
    default: 'py-8'
  }
})

const sidebarOpened = ref(true);
const currentUser = computed(() => store.state.user.data);
const showSpinner = ref(false);

function toggleSidebar() {
  sidebarOpened.value = !sidebarOpened.value;
}

onUnmounted(() => {
  window.removeEventListener("resize", handleSidebarOpened);
});

onMounted(() => {
  showSpinner.value = true;
  setTimeout(() => {
    store.dispatch("getUser");
    handleSidebarOpened();
    window.addEventListener("resize", handleSidebarOpened);
    showSpinner.value = false;
  }, 150);
});

function handleSidebarOpened() {
  sidebarOpened.value = window.outerWidth > 768;
}


</script>

<style scoped></style>

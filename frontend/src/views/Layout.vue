<template>
  <div>
    <div v-if="progress" class="progress">
      <div v-bind:style="{ width: this.progress + '%' }"></div>
    </div>
    <sidebar v-if="isAdmin"></sidebar>
    <main :class="{'user-main': !isAdmin}">
      <router-view></router-view>
      <shell v-if="isExecEnabled && isLogged && user.perm.execute" />
    </main>
    <prompts></prompts>
    <upload-files></upload-files>
  </div>
</template>

<script>
import { mapState, mapGetters } from "vuex";
import Sidebar from "@/components/Sidebar";
import Prompts from "@/components/prompts/Prompts";
import Shell from "@/components/Shell";
import UploadFiles from "../components/prompts/UploadFiles";
import { enableExec } from "@/utils/constants";

export default {
  name: "layout",
  data() {
    return {
      adminMain: "100%"
    }
  },
  components: {
    Sidebar,
    Prompts,
    Shell,
    UploadFiles,
  },
  computed: {
    ...mapGetters(["isLogged", "progress"]),
    ...mapState(["user"]),
    isExecEnabled: () => enableExec,
    isAdmin () {
      return this.user.perm.admin
    }
  },
  watch: {
    $route: function () {
      this.$store.commit("resetSelected");
      this.$store.commit("multiple", false);
      if (this.$store.state.show !== "success")
        this.$store.commit("closeHovers");
    },
  },
};
</script>
<style>
  .user-main { width: 100% !important}
</style>
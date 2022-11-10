<template>
  <div class="breadcrumbs">
    <component
      :is="element"
      :to="base || ''"
      :aria-label="$t('files.home')"
      :title="$t('files.home')"
    >
      <i class="material-icons">home</i>
    </component>
    <span v-for="(link, index) in items" :key="index">
      <span class="chevron"
        ><i class="material-icons">keyboard_arrow_right</i></span
      >
      <component :is="element" :to="link.url">{{ link.name }}</component>
    </span>
    <span v-if="!user.perm.admin" v-bind:style="{marginLeft: 'auto'}">
        <button
          @click="$store.commit('showHover', 'newDir')"
          class="action"
          :aria-label="$t('sidebar.newFolder')"
          :title="$t('sidebar.newFolder')"
        >
          <i class="material-icons">create_new_folder</i>
        </button>

        <button
          @click="$store.commit('showHover', 'newFile')"
          class="action"
          :aria-label="$t('sidebar.newFile')"
          :title="$t('sidebar.newFile')"
        >
          <i class="material-icons">note_add</i>
        </button>
      </span>
  </div>
</template>

<script>
import { mapState } from 'vuex';
export default {
  name: "breadcrumbs",
  props: ["base", "noLink"],
  computed: {
    ...mapState(["user"]),
    items() {
      const relativePath = this.$route.path.replace(this.base, "");
      let parts = relativePath.split("/");

      if (parts[0] === "") {
        parts.shift();
      }

      if (parts[parts.length - 1] === "") {
        parts.pop();
      }

      let breadcrumbs = [];

      for (let i = 0; i < parts.length; i++) {
        if (i === 0) {
          breadcrumbs.push({
            name: decodeURIComponent(parts[i]),
            url: this.base + "/" + parts[i] + "/",
          });
        } else {
          breadcrumbs.push({
            name: decodeURIComponent(parts[i]),
            url: breadcrumbs[i - 1].url + parts[i] + "/",
          });
        }
      }

      if (breadcrumbs.length > 3) {
        while (breadcrumbs.length !== 4) {
          breadcrumbs.shift();
        }

        breadcrumbs[0].name = "...";
      }

      return breadcrumbs;
    },
    element() {
      if (this.noLink !== undefined) {
        return "span";
      }

      return "router-link";
    },
  },
};
</script>

<style></style>
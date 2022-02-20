<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="custom-bg-color">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
      </q-toolbar>
      <div class="q-px-lg q-pt-xl q-mb-md">
        <div class="text-h3">Todo</div>
        <div class="text-subtitle1">{{ currentDate }}</div>
      </div>
      <q-img
        aria-label="header background image"
        src="~assets/header-bg.jpg"
        class="header-image absolute-top"
      />
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      :width="250"
      :breakpoint="650"
    >
      <q-scroll-area
        style="
          height: calc(100% - 192px);
          margin-top: 192px;
          border-right: 1px solid #ddd;
        "
      >
        <q-list padding>
          <EssentialLink
            v-for="link in essentialLinks"
            :key="link.title"
            v-bind="link"
          />
        </q-list>
      </q-scroll-area>

      <q-img
        class="absolute-top"
        src="~assets/header-bg.jpg"
        style="height: 192px"
        aria-label="Avatar background image"
      >
        <div class="absolute-bottom bg-transparent">
          <q-avatar size="56px" class="q-mb-sm">
            <img
              aria-label="Avatar image"
              src="https://cdn.quasar.dev/img/boy-avatar.png"
            />
          </q-avatar>
          <div class="text-weight-bold">Enoch Prince</div>
          <div>@enochprince7</div>
        </div>
      </q-img>
    </q-drawer>

    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from "components/EssentialLink.vue";

const linksList = [
  {
    title: "Todo",
    caption: "List of Todos",
    icon: "list_alt",
    link: "/",
  },
  {
    title: "Help",
    caption: "How to use the App",
    icon: "help",
    link: "/help",
  },
];

import { defineComponent, ref } from "vue";
import { date } from "quasar";

export default defineComponent({
  name: "MainLayout",

  components: {
    EssentialLink,
  },

  setup() {
    const leftDrawerOpen = ref(false);

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },

  computed: {
    currentDate() {
      let timestamp = Date.now();
      return date.formatDate(timestamp, "dddd, MMMM Do, YYYY");
    },
  },
});
</script>

<style lang="scss" scoped>
.custom-bg-color {
  background-color: #002a54; //overrides the primary color for the header
}
.header-image {
  height: 100%;
  z-index: -1;
  opacity: 0.2;
  filter: grayscale(100%);
}
</style>

<template>
  <a-layout-header :class="[headerTheme, 'admin-header']">
    <div :class="['admin-header-wide', layout, pageWidth]">
      <router-link
        v-if="isMobile || layout === 'head'"
        to="/"
        :class="['logo', isMobile ? null : 'pc', headerTheme]"
      >
        <img
          width="180"
          height="110"
          style="padding-bottom:45px"
          src="@/assets/img/logo.png"
        />
        <!-- 
        <h1 style="margin-left:8px; font-size: 25px; font-weight: bold; margin-top:5px" v-if="!isMobile">{{systemName}}</h1>
        -->
      </router-link>
      <a-divider v-if="isMobile" type="vertical" />
      <a-icon
        v-if="layout !== 'head'"
        class="trigger"
        :type="collapsed ? 'menu-unfold' : 'menu-fold'"
        @click="toggleCollapse"
      />
      <div
        v-if="layout !== 'side' && !isMobile"
        class="admin-header-menu"
        :style="`width: ${menuWidth};`"
      >
        <i-menu
          class="head-menu"
          :theme="headerTheme"
          mode="horizontal"
          :options="menuData"
          @select="onSelect"
        />
      </div>
      <div :class="['admin-header-right', headerTheme]">
        <a-tooltip
          class="header-item"
          title="Help documentation"
          placement="bottom"
        >
          <a href="https://github.com/dvconsultores/nearp2p/wiki" target="_blank">
            <a-icon
              type="question-circle-o"
              style="padding-left:20px; font-size: 16px;"
            />
          </a>
        </a-tooltip>
        <header-avatar class="header-item" />
      </div>
    </div>
  </a-layout-header>
</template>

<script>
import HeaderAvatar from "./HeaderAvatar";
import IMenu from "@/components/menu/menu";
import { mapState, mapMutations } from "vuex";

export default {
  name: "AdminHeader",
  components: { IMenu, HeaderAvatar },
  props: ["collapsed", "menuData"],
  data() {
    return {
      searchActive: false,
    };
  },
  computed: {
    ...mapState("setting", [
      "theme",
      "isMobile",
      "layout",
      "systemName",
      "lang",
      "pageWidth",
    ]),
    headerTheme() {
      if (
        this.layout == "side" &&
        this.theme.mode == "dark" &&
        !this.isMobile
      ) {
        return "light";
      }
      return this.theme.mode;
    },
    langAlias() {
      let lang = this.langList.find(item => item.key == this.lang);
      return lang.alias;
    },
    menuWidth() {
      const { layout, searchActive } = this;
      const headWidth = layout === "head" ? "100% - 188px" : "100%";
      const extraWidth = searchActive ? "600px" : "400px";
      return `calc(${headWidth} - ${extraWidth})`;
    }
  },
  methods: {
    toggleCollapse() {
      this.$emit("toggleCollapse");
    },
    onSelect(obj) {
      this.$emit("menuSelect", obj);
    },
    ...mapMutations("setting", ["setLang"])
  }
};
</script>

<style lang="less" scoped>
@import "index";
</style>

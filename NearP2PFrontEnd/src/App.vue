<template>
  <a-config-provider :locale="locale" :get-popup-container="popContainer">
    <router-view />
  </a-config-provider>
</template>

<script>
import { enquireScreen } from "./utils/util";
import { mapState, mapMutations } from "vuex";
import themeUtil from "@/utils/themeUtil";
import { getI18nKey } from "@/utils/routerUtil";
import Cookies from 'js-cookie'



export default {
  name: "App",
  data() {
    return {
      locale: {},
    };
  },
  created() {
    this.setHtmlTitle();
    const cookieLang = Cookies.get(process.env.VUE_APP_GLOBAL_LANG)||'US';
    // console.log(cookieLang);
    this.setLanguage(cookieLang);
    this.setLang(cookieLang);
    enquireScreen(isMobile => this.setDevice(isMobile));
  },
  mounted() {
    this.setWeekModeTheme(this.weekMode);
  },
  watch: {
    weekMode(val) {
      this.setWeekModeTheme(val);
    },
    lang(val) {
      this.setLanguage(val);
      this.setHtmlTitle();
    },
    $route() {
      this.setHtmlTitle();
    },
    "theme.mode": function(val) {
      let closeMessage = this.$message.loading(
        `You have selected the theme mode ${val}, Switching...`
      );
      themeUtil.changeThemeColor(this.theme.color, val).then(closeMessage);
    },
    "theme.color": function(val) {
      let closeMessage = this.$message.loading(
        `You have selected the theme color ${val}, Switching...`
      );
      themeUtil.changeThemeColor(val, this.theme.mode).then(closeMessage);
    },
    layout: function() {
      window.dispatchEvent(new Event("resize"));
    }
  },
  computed: {
    ...mapState("setting", ["layout", "theme", "weekMode", "lang"])
  },
  methods: {
    ...mapMutations("setting", ["setDevice", "setLang"]),
    setWeekModeTheme(weekMode) {
      if (weekMode) {
        document.body.classList.add("week-mode");
      } else {
        document.body.classList.remove("week-mode");
      }
    },
    setLanguage(lang) {
      this.$i18n.locale = lang;
      switch (lang) {
        case "CN":
          this.locale = require("ant-design-vue/es/locale-provider/zh_CN").default;
          break;
        case "HK":
          this.locale = require("ant-design-vue/es/locale-provider/zh_TW").default;
          break;
        case "US":
        default:
          this.locale = require("ant-design-vue/es/locale-provider/en_US").default;
          break;
      }
    },
    setHtmlTitle() {
      const route = this.$route;
      const key =
        route.path === "/"
          ? "home.name"
          : getI18nKey(route.matched[route.matched.length - 1].path);
      document.title = process.env.VUE_APP_NAME + " | " + this.$t(key);
    },
    popContainer() {
      return document.getElementById("popContainer");
    }
  }
};
</script>

<style lang="less" scoped>
#id {
}
</style>

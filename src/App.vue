<template>
  <div id="app" @keydown.tab="revertMenuSession" :style="{'background': backgroundColor}">
    <div
      v-show="$route.path.includes('publications')"
      class="app__overlay"
      @click="handleMenuBackgroundClickEvent"
    ></div>
    <transition appear>
      <the-nav-bar
        class="lefter"
        :class="{'lefter__mobile--menu-open': $mq === 'sm' && mobileMenuOpen}"
        :mobileMenuOpen="mobileMenuOpen"
        @revertMenuSession="revertMenuSession"
        @openContent="handleOpenContentEvent"
      ></the-nav-bar>
    </transition>

    <the-site-header
      headerTitle
      :backgroundColor="backgroundColor"
      :buttonSetToClose="buttonSetToClose"
      @openMenu="handleOpenMenu"
      @close="handleClose"
      @logoLinkActivated="handleLogoLinkActivation"
    ></the-site-header>

    <info-card
      v-bind="infoCardData"
      class="info-card"
      :class="{'info-card--closed': !infoCardOpen}"
    ></info-card>

    <transition name="component-fade" mode="out-in" v-if="!$route.path.includes('publications')">
      <router-view
        class="content-section"
        @click.native="revertMenuSession"
        @focus.native="revertMenuSession"
      ></router-view>
    </transition>

    <the-site-footer
      class="site-footer"
      :style="{'background': backgroundColor, 'box-shadow': `0 50px 30px 72px ${backgroundColor}`}"
    ></the-site-footer>
  </div>
</template>

<script>
import TheNavBar from "@/components/TheNavBar";
import Home from "@/views/Home";
import TheSiteFooter from "./components/TheSiteFooter";
import TheSiteHeader from "./components/TheSiteHeader";

import InfoCardData from "@/lib/dummy_data/data_infoCard.js";
import InfoCard from "@/components/InfoCard";

import styles from "@/lib/styles.js";

export default {
  components: {
    TheSiteHeader,
    TheSiteFooter,
    TheNavBar,
    Home,
    InfoCard
  },
  data: function() {
    return {
      mobileMenuOpen: false,
      infoCardOpen: true,
      styles: styles,
      infoCardData: InfoCardData
    };
  },
  computed: {
    buttonSetToClose: function() {
      return this.mobileMenuOpen || this.infoCardOpen;
    },
    backgroundColor: function() {
      return this.infoCardOpen && this.$mq === "sm"
        ? styles.backgroundColors.card
        : styles.backgroundColors.default;
    }
  },
  methods: {
    /** */
    handleOpenMenu: function() {
      this.mobileMenuOpen = true;
    },

    /** */
    handleClose: function() {
      if (this.mobileMenuOpen) this.mobileMenuOpen = false;
      if (this.infoCardOpen) this.infoCardOpen = false;
    },

    /** */
    handleMenuBackgroundClickEvent: function() {
      this.revertMenuSession();
    },

    /**
     * Process to handle logo click event
     */
    handleLogoLinkActivation: function() {
      this.$router.push("/");
      let visitCount = this.$store.getters["routerNav/getVisitCount"];
      if (visitCount > 0) {
        this.$store.dispatch("routerNav/resetVisitCount");
        this.$store.dispatch("menuTree/closeMenuExpansions");
      }
    },

    /**
     * Handles the event from the user requesting an article
     * @param {string} routerLinkLocation
     * @param {boolean} keyboardEvent
     */
    handleOpenContentEvent: function(routerLinkLocation, keyBoardEvent) {
      this.mobileMenuOpen = false;
      this.$store.dispatch("routerNav/resetVisitCount");
      if (keyBoardEvent) {
        this.$router.push(routerLinkLocation);
      }
      this.$store.dispatch("menuTree/closeMenuExpansions");
    },

    /**
     * Returns the app to the state that was before the menu session
     */
    revertMenuSession: function() {
      let visitCount = this.$store.getters["routerNav/getVisitCount"];
      // If menu session didn't include clicked publication links
      if (visitCount > 0) {
        this.$router.go(visitCount * -1);
        this.$store.dispatch("routerNav/resetVisitCount");
      } else if (this.$route.path.includes("publications")) {
        // if menu session began open b/c first visit to site was to collection
        this.$router.push("/");
        this.$store.dispatch("routerNav/resetVisitCount");
      }
      this.$store.dispatch("menuTree/closeMenuExpansions");
    }
  }
};
</script>

<style lang="scss">
@import "styles/_settings";
@import "styles/focusable";

* {
  margin: 0;
  padding: 0;
  color: black;
  list-style-type: none;
  font-weight: normal;
  margin-block-start: 0;
  margin-block-end: 0;
  font-size: 12px;
  text-decoration: none;
  @include bp-small {
    font-size: 0.9em;
  }
}

body {
  background: $color-default;
}

#app {
  position: absolute;
  width: $app-width-desktop; // 100vw adds a scroll bar to the bottom of page
  height: 100vh;

  color: #2c3e50;

  background: transparent;

  text-align: center;
  font-family: $font-stack-avenir;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;

  @include bp-small {
    width: $app-width-mobile;
  }
}

.app__overlay {
  width: 100%;
  height: 100%;
}

.lefter {
  position: fixed;
  top: 0;
  left: 0;
  width: $lefter-width-desktop;
  z-index: 10;
  margin-top: 100px;

  @include bp-small {
    transform: translateX(-100vw);
    @include transition($transition-overlay-duration ease-in);
    height: 100%;
    margin-top: 60px;
    width: $lefter-width-mobile;
  }
}

.lefter__mobile--menu-open {
  transform: translateX(0vw);
  // left: 0;
  @include transition($transition-overlay-duration ease-out);
}

.info-card {
  top: 80px;
  background: rgb(247, 247, 233);
  @include transition($transition-overlay-duration ease-in);
  @include bp-small {
    position: absolute;
    left: 0;
    padding: 12px;
    margin-bottom: 50px;
  }
}

.info-card--closed {
  transform: translateX(100vw);
  @include transition($transition-overlay-duration ease-in);

  @include bp-small {
    // transform: translateX(0vw);
  }
}

.content-section {
  top: 0;
  left: 0;
  width: calc(#{$app-width-desktop} - #{$lefter-width-desktop} * 1.5);
  padding: 120px 0 0 calc(#{$lefter-width-desktop} * 1.5);

  overflow-x: hidden;

  @include bp-medium {
    margin: 100px 10px 10px 10px;
    padding: 0px 0 0 calc(#{$lefter-width-desktop} * 1.5);
  }
  @include bp-small {
    margin: 72px 10px 10px 10px;
    width: calc(100vw - 20px);
    padding: 0;
  }
}

.site-footer {
  position: fixed;
  bottom: 0;
  width: 100%;
  height: $footer-height-desktop;
  background: $color-default;
  box-shadow: 0 50px 30px 72px $color-default;

  @include bp-small {
    height: $footer-height-mobile;
    font-size: 1.5em;
  }
}

.component-fade-enter-active,
.component-fade-leave-active {
  @include transition(opacity 0.3s ease);
}
.component-fade-enter,
.component-fade-leave-to {
  opacity: 0;
}
</style>
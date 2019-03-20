<template>
    <div
        id="app"
        @keydown.tab="revertMenuSession"
    >
        <div
            v-show="$route.path.includes('publications')"
            class="app__overlay"
            @click="handleMenuBackgroundClickEvent"
        ></div>
        <transition appear>
            <the-nav-bar
                class="lefter"

                :mobileMenuOpen="mobileMenuOpen"

                @revertMenuSession="revertMenuSession"
                @openContent="handleOpenContentEvent"
            ></the-nav-bar>
        </transition>

        <the-site-header
            headerTitle=""
            :backgroundColor="styles.backgroundColors.default"
            @openMenu="handleOpenMenu"
            @closeMenu="handleCloseMenu"
            @logoLinkActivated="handleLogoLinkActivation"
        ></the-site-header>

        <transition
            name="component-fade"
            mode="out-in"
            v-if="!$route.path.includes('publications')"
        >
            <router-view
                class="content-section"
                @click.native="revertMenuSession"
                @focus.native="revertMenuSession"
            >
            </router-view>
        </transition>

        <the-site-footer class="site-footer"></the-site-footer>
    </div>
</template>

<script lang="ts">
import {Component, Vue} from 'vue-property-decorator';
import TheNavBar from '@/components/TheNavBar';
import Home from '@/views/Home';
import TheSiteFooter from './components/TheSiteFooter';
import TheSiteHeader from './components/TheSiteHeader';

import styles from '@/lib/styles.js';

@Component({
    components: {
        TheSiteHeader,
        TheSiteFooter,
        TheNavBar,
        Home
    }
})

export default class App extends Vue {
    private mobileMenuOpen = false;
    private styles = styles;

    constructor() {
        super();
    }

    private handleOpenMenu() {
        this.mobileMenuOpen = true;
    }

    private handleCloseMenu() {
        this.mobileMenuOpen = false;
    }

    private handleMenuBackgroundClickEvent(): void {
        this.revertMenuSession();
    }

    // Process to handle logo click event
    private handleLogoLinkActivation(): void {
        this.$router.push('/');
        let visitCount = this.$store.getters['routerNav/getVisitCount'];
        if (visitCount > 0) {
            this.$store.dispatch('routerNav/resetVisitCount');
            this.$store.dispatch('menuTree/closeMenuExpansions');
        }
    }

    // Handles the event from the user requesting an article
    private handleOpenContentEvent(routerLinkLocation: string, keyBoardEvent: boolean) {
        this.$store.dispatch('routerNav/resetVisitCount');
        if (keyBoardEvent) {
            this.$router.push(routerLinkLocation);
        }
        this.$store.dispatch('menuTree/closeMenuExpansions');
    }

    // Returns the app to the state that was before the menu session
    private revertMenuSession(): void {
        let visitCount = this.$store.getters['routerNav/getVisitCount'];
        // If menu session didn't include clicked publication links
        if (visitCount > 0) {
            this.$router.go(visitCount * -1);
            this.$store.dispatch('routerNav/resetVisitCount');
        } else if (this.$route.path.includes('publications')) { // if menu session began open b/c first visit to site was to collection
            this.$router.push('/');
            this.$store.dispatch('routerNav/resetVisitCount');
        }
        this.$store.dispatch('menuTree/closeMenuExpansions');
    }
}
</script>

<style lang="scss">
    @import 'styles/_settings';
    @import 'styles/focusable';

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
        @media screen and (max-width: $breakSmall) {
            font-size: 0.9em;
        }
    }

    body {
        background: $bgColor;
    }

    #app {
        position: absolute;
        width: $appWidth; // 100vw adds a scroll bar to the bottom of page
        height: 100vh;

        color: #2c3e50;

        background: transparent;

        text-align: center;
        font-family: $sansSerifFont;
        -moz-osx-font-smoothing: grayscale;
        -webkit-font-smoothing: antialiased;

        @media screen and (max-width: $breakSmall) {
            width: 100vw;
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
        width: $lefterWidth;
        z-index: 10;
    }

    .content-section {
        top: 0;
        left: 0;
        width: calc(#{$appWidth} - #{$lefterWidth} * 1.5);
        padding: 120px 0 0 calc(#{$lefterWidth} * 1.5);
        // padding: 0 0 0 calc(#{$lefterWidth} * 1.5);

        overflow-x: hidden;

        @media screen and (max-width: $breakSmall) {
            padding: 72px 0 0 0;
            width: 100%;
        }
        @media screen and (min-width: $breakSmall) and (max-width: $breakMedium) {
            padding: 100px 0 0 calc(#{$lefterWidth} * 1.5);
        }
    }

    .site-footer {
        position: fixed;
        bottom: 0;
        width: 100%;
        height: $footerHeight;
        background: #faf7f7;
        box-shadow: 0 50px 30px 72px #faf7f7;

        @media screen and (max-width: $breakSmall) {
            font-size: 1.5em;
        }
    }

    .component-fade-enter-active, .component-fade-leave-active {
        transition: opacity .3s ease;
    }
    .component-fade-enter, .component-fade-leave-to {
        opacity: 0;
    }
</style>
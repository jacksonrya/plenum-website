<template>
    <vue-headroom
            :disabled="true || $mq !== 'lg' || this.$route.path.includes('content')"
            :upTolerance="$mq !== 'lg' ? 0 : null"
            :downTolerance="$mq !== 'lg' ? 5 : null"
            :classes="{
                initial :   'site-headroom',
                pinned :    'site-headroom--pinned',
                unpinned :  'site-headroom--unpinned',
                top :       'site-headroom--top',
                notTop :    'site-headroom--not-top',
                bottom :    'site-headroom--bottom',
                notBottom : 'site-headroom--not-bottom'
            }"
            class="site-header-container"
            :class="{'site-headroom--hidden': this.$route.path.includes('content')}"
            :speed="500"
            :z-index="9"
    >
        <div class="site-header__background" :style="{'background': backgroundColor, 'box-shadow': `0 -50px 30px 72px ${backgroundColor}`}"></div>
        <header class="site-header"> 
            <the-logo
                class="site-header__logo"
                @logoLinkActivated="logoLinkActivated"
            ></the-logo>
            <div class="site-header__title-container">
                <div class="website-header">
                    <router-link
                        to="/"
                        title="Return to Home"
                        tabindex="-1"
                    >
                        <img class="site-header__title" src="@/assets/plenum-title.svg">
                    </router-link>
                    <img
                        class="site-header__subtitle"
                        :class="{'site-header__subtitle--hidden': $mq === 'sm' || this.$route.path.includes('publications')}"
                        src="@/assets/plenum-subtitle.svg"
                    >
                </div>
            </div>
            <the-menu-button 
                v-show="$mq == 'sm'"
                class="site-header__menu-button"
                @menuClick="handleOpenMenu"
                @closeClick="handleCloseMenu"
            ></the-menu-button>
        </header>
    </vue-headroom>
</template>

<script lang="ts">
import {Component, Prop, Vue} from 'vue-property-decorator';
import TheMenuButton from '@/components/TheMenuButton';
import TheLogo from './TheLogo';


@Component({
    components: {
        TheMenuButton,
        TheLogo
    },
    props: {
        backgroundColor: String
    }
})

// The header for the entire site
// Disappears when
// Required properties:
export default class TheSiteHeader extends Vue {
    constructor() {
        super();
    }

    handleOpenMenu() {
        this.$emit('openMenu')
    }
    handleCloseMenu() {
        this.$emit('closeMenu')
    }

    // Emits logo click event to parent
    private logoLinkActivated() {
        this.$emit('logoLinkActivated')
    }
}

</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .site-header-container {
        @extend header;
        // box-shadow: 0 -50px 30px 72px #faf7f7;
        // background: $bgColor;
        background: transparent;
    }

    .site-headroom--hidden {
        display: none;
    }

    .site-headroom--not-top.site-headroom--pinned > .site-header {
        // border-bottom: 1px solid black;
    }

    .site-header {
        position: absolute;
        //width: calc(#{$appWidth} - #{$headerHeight} - #{$rightPageNavWidth});
        height: 100%;
        display: flex;
        justify-content: flex-start;

        padding-right: calc(#{$lefterWidth / 2});

        @media screen and (max-width: $breakSmall) {
            width: 100%;
        }
        @media screen and (min-width: $breakSmall) and (max-width: $breakMedium) {
            width: 100%;
            top: 10px;
        }
    }

    .site-header__background {
        position: absolute;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100px;
        background: $bgColor;
        box-shadow: 0 -50px 30px 72px $bgColor;

        @media screen and (max-width: $breakSmall) {
            height: 60px;
        }
    }

    .site-header__logo {
        @extend logo;
    }

    .site-header__title-container {
        position: relative;
        left: 0;
        top: 50%;
        transform: translateY(-50%);
        height: fit-content;
    }

    .site-header__title-container a:focus {
        outline: none;
    }

    .site-header__title-container > * {
        display: inline-block;
        vertical-align: middle;
    }

    .site-header__title {
        width: 6em;
        margin-right: 1em;
    }

    .site-header__subtitle {
        position: relative;
        top: -0.2em;
        width: 20em;

        opacity: 1;
    }

    .site-header__subtitle--hidden {
        display: none;
    }

    // TODO: make the same size as the logo
    .site-header__menu-button {
        position: fixed;
        top: 50%;
        transform: translateY(-50%);
        right: 20px;
        width: 25px;
        height: 25px;
    }
</style>
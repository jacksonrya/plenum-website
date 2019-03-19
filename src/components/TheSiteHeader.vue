<template>
    <vue-headroom
            :disabled="true || $mq !== 'lg' || this.$route.path.includes('content')"
            :upTolerance="$mg !== 'lg' ? 0 : null"
            :downTolerance="$mg !== 'lg' ? 5 : null"
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
        <header class="site-header">
            <div class="site-header__title-container">
                <router-link
                    to="/"
                    title="Return to Home"
                    tabindex="-1"
                >
                    <img class="site-header__title" src="@/assets/plenum-title.svg">
                </router-link>
                <img
                    :class="{'site-header__subtitle--hidden': $mq === 'sm' || this.$route.path.includes('publications')}"
                    class="site-header__subtitle"
                    src="@/assets/plenum-subtitle.svg"
                >
            </div>
            <the-menu-button 
                v-show="$mq == 'sm'"
                class="site-header__menu-button"
                @buttonClick="handleMenuButtonClick"
            ></the-menu-button>
        </header>
    </vue-headroom>
</template>

<script lang="ts">
import {Component, Prop, Vue} from 'vue-property-decorator';
import TheMenuButton from '@/components/TheMenuButton';

@Component({
    components: {
        TheMenuButton
    },
})

// The header for the entire site
// Disappears when
// Required properties:
export default class TheSiteHeader extends Vue {
    constructor() {
        super();
    }

    handleMenuButtonClick() {
        this.$emit('menuButtonClick')
        console.log('menu button clicked')
    }
}

</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .site-header-container {
        @extend header;
        box-shadow: 0 -50px 30px 72px #faf7f7;
        background: $bgColor;
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
        left: $lefterWidth;

        padding-right: calc(#{$lefterWidth / 2});

        @media screen and (max-width: $breakSmall) {
            width: 100%;
            left: 72px;
            top: 6px;
        }
        @media screen and (min-width: $breakSmall) and (max-width: $breakMedium) {
            width: 100%;
            top: 10px;
        }
    }

    .site-header__title-container {
        position: absolute;
        left: 0;
        top: 50%;
        transform: translateY(-50%);
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
        opacity: 0;

        transition: opacity 0.3s ease;
    }

    // TODO: make the same size as the logo
    .site-header__menu-button {
        position: fixed;
        top: 20px;
        right: 20px;
        width: 25px;
        height: 25px;
    }
</style>
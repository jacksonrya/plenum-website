<template>
    <div
        class="title-card__container"
        :class="{'title-card__container--hidden': hidden}"
    >
        <h1 class="title-card__title">
            {{ title }}
            <br>
            <span class="title-card__subtitle">
                {{ subtitle }}
            </span>
        </h1>

        <address class="title-card__author">
            {{ author }}
        </address>
    </div >
</template>

<script lang="ts">
    import {Component, Prop, Vue} from 'vue-property-decorator';

    @Component({
        components: {
        },
    })

    export default class TextArticleTitleCard extends Vue {
        @Prop() private title!: string;
        @Prop() private subtitle!: string;
        @Prop() private author!: string;
        @Prop({ default: false }) private hidden!: boolean;

        constructor() {
            super();
        }
    }
</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .title-card__container {
        position: relative;
        left: 0;
        width: fit-content;
        max-width: 60vw;
        padding: 20px;
        z-index: 0;

        background: $bgColor;

        text-align: left;

        box-shadow: 8px 8px 10px 2px #00000029;
    }

    .title-card__container--hidden {
        background: transparent;

        opacity: 0;
        transition: opacity 150ms ease;
    }

    .title-card__container--focused {
        outline: 3px solid black;
    }

    .title-card__title,
    .title-card__subtitle,
    .title-card__author {
        font-weight: normal;
        font-style: normal;
    }

    .title-card__title {
        font-size: 3em;
        line-height: 1.5em;
    }

    .title-card__subtitle {
        display: block;

        margin-left: 1.5em;
        font-size: 0.8em;
        line-height: 1.2em;

        text-align: left;
    }

    .title-card__author {
        margin-top: 15px;

        font-size: 1.8em;
        font-style: italic;
        text-align: left;
    }
</style>
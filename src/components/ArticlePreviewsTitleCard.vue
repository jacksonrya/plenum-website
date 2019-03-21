<template>
    <div>
        <h1 class="title">{{article.content_title}}</h1>
        <div
            v-if="article.subtitle"
            class="subtitle"
        >
            {{article.subtitle}}
        </div>
    </div>
</template>

<script>
/**
 * Submenu associated with a unique main menu entry
 */
export default {
    props: {
        article: {
            type: Object,
            default: function() {
                return {}
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    .title {
        font-weight: lighter;
        font-size: 2.1em;
        line-height: 1.8em;
    }

    .subtitle {
        font-weight: lighter;
        padding-left: 2em;
        font-size: 1.9em;
        line-height: 1.4em;
    }

    .author {
        margin-top: 3px;

        font-size: 1.5em;
        font-weight: lighter;
        font-style: italic;
    }


</style>
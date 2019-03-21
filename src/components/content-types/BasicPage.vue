<template>
    <article class="basic-page">
        <h1 class="basic-page__title">
            {{page.title}}
        </h1>
        <section
            v-for="(section, index) in page.body"
            :key="`basic-page-${index}`"
            class="basic-page__section"
            v-html="section.processed"
        ></section>
        <hr class="basic-page__divider">
    </article>
</template>

<script>
export default {
    props: {
        page: {
            type: Object,
            default: function() {
                return {}
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    @import '../../styles/basic-page';
</style>

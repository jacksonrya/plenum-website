<template>
    <div class="menu-button">
        <div v-if="closed" @click="handleMenuButtonClick">
            <img class="menu-button" src="@/assets/menu-button.svg">
        </div>
        <div v-else @click="handleCloseButtonClick" src="@/assets/close-button.svg">
            <img class="close-button" src="@/assets/close-button.svg">
        </div>
    </div>
</template>

<script>
export default {
    props: {
        // Whether or not the menu is closed
        closed: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        handleMenuButtonClick: function() {
            this.$emit("menuClick")
        },
        handleCloseButtonClick: function() {
            this.$emit("closeClick")
        }
    }
}
</script>

<style lang="scss" scoped>
    // @import '../styles/_settings';
</style>
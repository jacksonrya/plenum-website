<template>
    <div @click="handleMenuButtonClick">
        <img class="menu-button" src="@/assets/menu-button.svg">
    </div>
</template>

<script lang="ts">
import {Component, Prop, Vue} from 'vue-property-decorator';

@Component({
    components: {

    },
})

// The menu button for non-desktop scenarios
// Required properties:
export default class TheMenuButton extends Vue {
    constructor() {
        super();
    }

    handleMenuButtonClick() {
        this.$emit("buttonClick")
    }
}

</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .menu-button {
        position: relative;
        width: 100%;
        height: 100%;
    }
</style>
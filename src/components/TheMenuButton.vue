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

<script lang="ts">
import {Component, Prop, Vue} from 'vue-property-decorator';

@Component({
    components: {
        
    },
    props: {
        closed: {
            type: Boolean,
            default: false
        }
    }
})

// The menu button for non-desktop scenarios
// Required properties:
export default class TheMenuButton extends Vue {
    constructor() {
        super();
    }

    handleMenuButtonClick() {
        // this.closed = false;
        this.$emit("menuClick")
    }

    handleCloseButtonClick() {
        // this.closed = true;
        this.$emit("closeClick")
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
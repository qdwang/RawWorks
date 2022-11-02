<template>
    <div class="container" v-if="webgl_instance">
        gamma {{ gamma }}
        <n-slider v-model:value="gamma" :step="0.01" :min="0" :max="10" :tooltip="false" />
        exposure {{ exposure }}
        <n-slider v-model:value="exposure" :step="0.01" :min="-3" :max="3" :tooltip="false" />
    </div>
</template>

<script>
import { updateUniform, quickraw } from "../quickraw";

let timeout = 1;
const lag = 300;

export default {
    props: ['webgl_instance'],
    data() {
        return {
            gamma: 2.22,
            exposure: 0
        }
    },
    watch: {
        exposure(v) {
            timeout = updateUniform(this.webgl_instance, 'uniform1f', 'exposure', v, pixels => {
                const histogram_data = quickraw.calc_histogram(pixels);
                this.$emit("histogram_load", histogram_data);
            }, timeout, lag);
        },
        gamma(v) {
            timeout = updateUniform(this.webgl_instance, 'uniform1f', 'gamma', 1 / v, pixels => {
                const histogram_data = quickraw.calc_histogram(pixels);
                this.$emit("histogram_load", histogram_data);
            }, timeout, lag);
        }
    }
}
</script>
<style scoped>
.container {
    margin-bottom: .5rem;
}
</style>
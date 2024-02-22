<template>
    <!-- <div > -->
    <camera ref="camera" class="camera" :key="cam" :resolution="{ width: this.windowWidth, height: this.windowHeight }"
        autoplay>
        <Vision :file="file" />
    </camera>
    <Controller @CV="logEvent" class="controller" />

    <!-- </div> -->
</template>
<script>
import { defineComponent } from "vue";
import Camera from "simple-vue-camera";
import Controller from "./CameraControls.vue";
import Vision from "./ComputerVision.vue";
import { ref } from 'vue'
export default defineComponent({
    setup() {
        const camera = ref(null);
        const file = ref(null);

        const snapshot = async () => {
            const blob = await camera.value.snapshot();
            const fileObject = new File([blob], 'snapshot.jpg', { type: 'image/jpeg' });

            return fileObject

        }
        return {
            camera,
            file,
            snapshot
        }
    },
    components: {
        Camera,
        Controller,
        Vision
    },
    data() {
        return {
            windowHeight: window.innerHeight,
            windowWidth: window.innerWidth,
            cam: 0,
            file: null
        }
    },
    created() {
        window.addEventListener("resize", this.camResize);
    },
    destroyed() {
        window.removeEventListener("resize", this.camResize);
    },
    methods: {
        async logEvent(e) {
            console.log(e)
            this.file = await this.snapshot();
            console.log(this.file);

        },
        camResize(e) {
            this.windowHeight = e.currentTarget.innerHeight;
            this.windowWidth = e.currentTarget.innerWidth

            this.cam++
            console.table([{ height: this.windowHeight }, { width: this.windowWidth }])

        }
    }
});


</script>
<style>
.camera {
    max-width: 100%;
    max-height: 100vh;
    width: auto;
    height: auto;
    object-fit: cover;

}

.controller {
    position: absolute;
    top: 1%;
    left: 1%;
}
</style>
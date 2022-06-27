<script>
import LoadingImage from './../assets/loading.gif';

export default {
    data() {
        return {
            imageLink: LoadingImage
        };
    }
};
</script>

<template>
    <div class="row">
        <div class="col d-flex justify-content-center">
            <img :src="imageLink"/>
        </div>
    </div>
</template>
<script>
import TokenItem from './TokenItem.vue';

export default {
    props: ['token'],
    emits: ['sendToken', 'contestToken'],
    components: {
        TokenItem
    }
}
</script>

<template>
    <TokenItem :token="token">
        <div class="card-body">
            <div class="row">
                <div class="col d-flex justify-content-start">
                    <a href="#" class="btn btn-primary" @click="$emit('sendToken', token.tokenId)">Send</a>
                </div>
                <div class="col d-flex justify-content-end">
                    <a href="#" class="btn btn-danger" @click="$emit('contestToken', token.tokenId)">Contest!</a>
                </div>
            </div>
        </div>
    </TokenItem>
</template>
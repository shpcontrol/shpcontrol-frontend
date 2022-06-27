<script>
import TokenItemControlable from './TokenItemControlable.vue';
import PaginationBar from './PaginationBar.vue';

export default {
    props: ['tokens', 'page', 'perPage'],
    emits: ['changeTokenListPage', 'sendToken', 'contestToken'],
    components: {
        TokenItemControlable,
        PaginationBar
    },
    computed: {
        tokensOnPage() {
            return this.tokens.slice((this.page - 1) * this.perPage, this.page * this.perPage);
        }
    }
}
</script>

<template>
    <div class="row">
        <TokenItemControlable v-for="token in tokensOnPage" 
            :key="token.tokenId" 
            :token="token" 
            @sendToken="$emit('sendToken', $event)"
            @contestToken="$emit('contestToken', $event)"></TokenItemControlable>
    </div>
    <PaginationBar 
        :curPage=page 
        :items=tokens 
        :itemsPerPage=perPage 
        :pageDelta=5
        @changeCurPage="$emit('changeTokenListPage', $event)"></PaginationBar>
</template>
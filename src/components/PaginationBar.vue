<script>
/* eslint-disable */
export default {
    props: ['curPage', 'items', 'itemsPerPage', 'pageDelta'],
    emits: ['changeCurPage'],
    computed: {
        maxPage() {
            let maxPage = Math.floor(this.items.length / this.itemsPerPage);
            if ((this.items.length % this.itemsPerPage) > 0) {
                maxPage += 1;
            }
            return maxPage
        },
        pages() {
            let items = [];
            for(let i = Math.max(this.curPage - this.pageDelta, 1); i <= Math.min(this.curPage + this.pageDelta, this.maxPage); ++i) {
                items.push(i);
            }
            return items;
        }
    }
}
</script>

<template>
    <div class="row">
        <div class="col-12 d-flex align-items-center justify-content-center">
            <ul class="pagination">
                <li class="page-item" :class="{active: page === curPage}" v-for="page in pages" :key="page">
                    <a class="page-link" href="#" @click="$emit('changeCurPage', page)">{{page}}</a>
                </li>
            </ul>
        </div>
    </div>
</template>
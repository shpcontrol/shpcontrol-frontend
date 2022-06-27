<script>
import LoadingImage from './../assets/loading.gif';
import TokenItemPlaceholder from './TokenItemPlaceholder.vue';

async function mounted() {
    let image = new Image();
    image.src = this.token.tokenJSON.image;
    image.onload = () => {
        this.imageLink = image.src;
    };
}

export default {
    props: ['token'],
    components: {
        TokenItemPlaceholder
    },
    data() {
        return {
            imageLink: LoadingImage
        }
    },
    methods: {
        getAttribute(name) {
            return this.token.tokenJSON.attributes.filter((item) => {return item.trait_type === name;})[0].value;
        }
    },
    computed: {
        sheepContestBonus() {
            return this.getAttribute('Sheep Contest Bonus');
        },
        sex() {
            return this.getAttribute('Sex');
        },
        bloodGroup() {
            return this.getAttribute('Blood Group');
        },
        zodiacSign() {
            return this.getAttribute('Zodiac Sign');
        },
        dateOfBirth() {
            return (new Date(this.getAttribute('Date of Birth') * 1000)).toISOString().slice(0, 10);
        }
    },
    mounted
}
</script>

<template>
    <TokenItemPlaceholder 
        :imageLink="imageLink"
        :tokenId="token.tokenId"
        :name="token.tokenJSON.name"
        :description="token.tokenJSON.description"
        :sheepContestBonus="sheepContestBonus"
        :sex="sex"
        :bloodGroup="bloodGroup"
        :zodiacSign="zodiacSign"
        :dateOfBirth="dateOfBirth">
        <slot></slot>
    </TokenItemPlaceholder>
</template>
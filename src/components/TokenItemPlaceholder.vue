<script>
export default {
    props: ['imageLink', 'tokenId', 'name', 'description', 'sheepContestBonus', 'sex', 'bloodGroup', 'zodiacSign', 'dateOfBirth']
}
</script>

<template>
    <div class="col-4 mb-4">
        <div class="card">
            <img :src="imageLink" class="card-img-top" :alt="'Image of token ' + tokenId"/>
            <div class="card-body">
                <h5 class="card-title">{{name}}</h5>
                <p class="card-text">{{description}}</p>
            </div>
                <ul class="list-group list-group-flush">
                <li class="list-group-item">Sheep Contest Bonus: {{sheepContestBonus}}</li>
                <li class="list-group-item">Sex: {{sex}}</li>
                <li class="list-group-item">Blood Group: {{bloodGroup}}</li>
                <li class="list-group-item">Zodiac Sign: {{zodiacSign}}</li>
                <li class="list-group-item">Date Of Birth: {{dateOfBirth}}</li>
            </ul>
            <slot></slot>
        </div>
    </div>
</template>
<script>
import SheepLogo from './../assets/sheep.png';

export default {
    props: ['account', 'tokens', 'synced', 'syncedPercent'],
    emits: ['onUpdate'],
    data() {
        return {
            sheepLogo: SheepLogo
        }
    }
}
</script>

<template>
    <nav class="navbar navbar-expand-md navbar-light bg-light mb-4">
    <div class="container-fluid">
        <a class="navbar-brand" href="#">
            <img :src="sheepLogo" alt="" width="30" height="24">
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav">
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                    Account
                </a>
                <ul class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
                    <li><a class="dropdown-item disabled" href="#" tabindex="-1" aria-disabled="true">Address: {{account}}</a></li>
                    <li><a class="dropdown-item disabled" href="#">
                        <span :style="{color: synced ? 'green': 'red'}">● {{syncedPercent}}%</span> Assets: {{tokens.length}}
                    </a></li>
                    <li v-if="synced"><a class="dropdown-item" href="#" @click="$emit('onUpdate')">Update</a></li>
                </ul>
            </li>
        </ul>
        </div>
    </div>
    </nav>
</template>
<script>
import axios from 'axios';
import LoadingImage from './../assets/loading.gif';
import TokenItem from './TokenItem.vue';
import TokenItemPlaceholder from './TokenItemPlaceholder.vue';
import ContractMixin from './../ContractMixin.vue';


async function mounted() {
    this.neededOpponents = await this.getContestCountOfMembersToCheck(this.contestContract, this.account);
    this.opponents = await this.getContestOpponentsInfo();
}

export default {
    mixins: [ContractMixin],
    emits: ['close', 'contest'],
    props: ['account', 'contract', 'contestContract'],
    components: {
        TokenItem,
        TokenItemPlaceholder
    },
    data() {
        return {
            opponents: [],
            neededOpponents: 0,
            waitOpponentImage: LoadingImage
        };
    },
    methods: {
        async getContestOpponentInfo(i) {
            let memberInfo = undefined;
            try {
                memberInfo = await this.contestContract.methods.getMemberInfo(i).call({from: this.account});
            } catch (error) {
                console.log('Contest: failed to get member', i, 'info');
                throw error;
            }
            let address = memberInfo[0];
            let tokenId = memberInfo[1];
            let tokenURI = await this.getTokenURI(this.contract, this.account, tokenId);

            let response = undefined;
            try {
                response = await axios.get(tokenURI);
            } catch (error) {
                console.log('Contest: network error with token', tokenId, 'tokenURI', tokenURI);
                throw error;
            }
            return {address: address, tokenId: tokenId, tokenJSON: response.data};
        },
        async getContestOpponentsInfo() {
            let opponentsInfoTasks = [];
            let contestCountOfMembers = await this.getContestCurrentCountOfMembers(this.contestContract, this.account);
            for (let i = 0; i < contestCountOfMembers; ++i) {
                opponentsInfoTasks.push(this.getContestOpponentInfo(i));
            }
            try {
                return await Promise.all(opponentsInfoTasks);
            } catch (error) {
                console.log('Contest: catch error from getContestOpponentInfo');
                return [];
            }
        }
        
    },
    computed: {
        opponentsToStart() {
            return this.neededOpponents - this.opponents.length;
        }
    },
    mounted
}
</script>

<template>
    <div class="modal" tabindex="-1" style="display: block">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Your opponents have put up against you</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close" @click="$emit('close')"></button>
                </div>
                <div class="modal-body">
                    <div class="row">
                        <TokenItem v-for="opponent in opponents" :key=opponent.tokenId :token=opponent>
                            <div class="card-body">
                                <div class="row">
                                    <div class="col-12">
                                        Opponent address: {{opponent.address}}
                                        <p v-if="opponent.address === this.account" class="text-success">
                                            * It's you!
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </TokenItem>
                        <TokenItemPlaceholder v-for="waitOpponentNum in opponentsToStart" :key=waitOpponentNum
                            :imageLink="waitOpponentImage"
                            tokenId="???"
                            name="???"
                            description="???"
                            sheepContestBonus="???"
                            sex="???"
                            bloodGroup="???"
                            zodiacSign="???"
                            dateOfBirth="???"></TokenItemPlaceholder>
                    </div>
                    <div class="row">
                        <div class="col-12 d-flex justify-content-center">
                            <p class="text-primary">Contest will be finished when we got yet {{opponentsToStart}} sheep(s). You're ready?</p>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-danger" @click="$emit('contest')">Yea, let's try!</button>
                </div>
            </div>
        </div>
    </div>
</template>
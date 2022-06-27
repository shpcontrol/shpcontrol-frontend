<script>
import Web3 from 'web3/dist/web3.min.js'
import axios from 'axios';
import ContractMixin from './ContractMixin.vue';
import LoadingPlaceholder from './components/LoadingPlaceholder.vue'
import TopInfo from './components/TopInfo.vue';
import TokenList from './components/TokenList.vue';
import TokenSend from './components/TokenSend.vue';
import TokenContest from './components/TokenContest.vue';
import {TOKEN_ABI, TOKEN_ADDRESS, CONTEST_ABI, CONTEST_ADDRESS} from './contractInfo.js';


async function mounted() {
    console.log('Try to detect ethereum wallet');
    let provider = undefined;
    if (window.ethereum) {
        console.log('Have window.ethereum');
        provider = window.ethereum;
    } else if (Web3.givenProvider) {
        console.log('Have Web3.givenProvider');
        provider = Web3.givenProvider;
    } else {
        console.log('Don\'t have any wallet');
    }

    if (provider !== undefined) {
        this.web3 = new Web3(provider);
        try {
            console.log('Request accounts');
            let accounts = await this.web3.eth.requestAccounts();
            console.log('Received ', accounts);
            this.account = accounts[0];
        } catch (error) {
            console.log('Access to wallet denied');
        }
        this.contract = new this.web3.eth.Contract(TOKEN_ABI, TOKEN_ADDRESS);
        this.contestContract = new this.web3.eth.Contract(CONTEST_ABI, CONTEST_ADDRESS);

        await this.updateMyTokens();
    }
}

export default {
    name: 'SheepApp',
    mixins: [ContractMixin],
    data() {
        return {
            web3: undefined,
            account: undefined,
            contract: undefined,
            contestContract: undefined,
            tokens: [],
            tokenListPage: 1,
            tokenIdToSend: undefined,
            tokenIdToContest: undefined,
            tokensUpdateBatch: 6,
            synced: false
        };
    },
    computed: {
        isLoading() {
            return this.account === undefined;
        }
    },
    components: {
        LoadingPlaceholder,
        TopInfo,
        TokenList,
        TokenSend,
        TokenContest
    },
    methods: {
        async updateTokenI(i) {
            let tokenOwner = await this.ownerOf(this.contract, this.accounts, i);
            if (tokenOwner === this.account) {
                let tokenURI = await this.getTokenURI(this.contract, this.account, i);
                try {
                    let response = await axios.get(tokenURI);
                    return {tokenId: i, tokenJSON: response.data};
                } catch (error) {
                    console.log('Network error with token', i, ', tokenURI', tokenURI);
                }
            }
            return undefined;
        },
        async updateMyTokens() {
            function notUndefined(item) {
                return item !== undefined;
            }
            this.synced = false;
            this.changeTokenListPage(1);
            this.tokens.splice(0);

            let updateTokenITasks = [];
            let totalSupply = await this.getTotalSupply(this.contract, this.account);
            for (let i = 0; i < totalSupply; ++i) {
                updateTokenITasks.push(this.updateTokenI(i));
                if (updateTokenITasks.length >= this.tokensUpdateBatch) {
                    let tokensBatch = await Promise.all(updateTokenITasks);
                    this.tokens = [...this.tokens, ...tokensBatch.filter(notUndefined)];
                    updateTokenITasks.splice(0);
                }
            }
            if (updateTokenITasks.length > 0) {
                let tokensBatch = await Promise.all(updateTokenITasks);
                this.tokens = [...this.tokens, ...tokensBatch.filter(notUndefined)];
            }
            this.synced = true;
        },
        changeTokenListPage(page) {
            this.tokenListPage = page;
        },
        displaySendTokenDialog(tokenIdToSend) {
            this.tokenIdToSend = tokenIdToSend;
        },
        async sendToken(recipientAddress) {
            let tokenId = this.tokenIdToSend;
            this.displaySendTokenDialog(undefined);
            if ((await this.safeTransferFrom(this.web3, this.contract, this.account, recipientAddress, tokenId)) === false) {
                return;
            }
            this.changeTokenListPage(1);
            this.tokens = this.tokens.filter((item) => {return item.tokenId !== tokenId;});
        },
        displayContestTokenDialog(tokenIdToContest) {
            this.tokenIdToContest = tokenIdToContest;
        },
        async contestToken() {
            let tokenId = this.tokenIdToContest;
            this.displayContestTokenDialog(undefined);
            if ((await this.safeTransferFrom(this.web3, this.contract, this.account, CONTEST_ADDRESS, tokenId)) === false) {
                return;
            }
            this.changeTokenListPage(1);
            this.tokens = this.tokens.filter((item) => {return item.tokenId !== tokenId;});
        }
    },
    mounted
}
</script>

<template>
    <TopInfo v-if="!isLoading" :account="account" :tokens="tokens" @onUpdate="updateMyTokens" :synced="synced"></TopInfo>
    <LoadingPlaceholder v-if="isLoading || tokens.length == 0"></LoadingPlaceholder>
    <template v-else>
        <TokenList 
            :tokens="tokens"
            :page="tokenListPage" 
            :perPage="tokensUpdateBatch" 
            @changeTokenListPage="changeTokenListPage" 
            @sendToken="displaySendTokenDialog"
            @contestToken="displayContestTokenDialog"></TokenList>
        <TokenSend v-if="tokenIdToSend !== undefined" 
            @close="displaySendTokenDialog(undefined)" 
            @send="sendToken" 
            :web3="web3"></TokenSend>
        <TokenContest v-if="tokenIdToContest !== undefined" 
            @close="displayContestTokenDialog(undefined)" 
            @contest="contestToken"
            :account="account" 
            :contract="contract" 
            :contestContract="contestContract"></TokenContest>
    </template>
</template>

<style>
@import'~bootstrap/dist/css/bootstrap.css'
</style>
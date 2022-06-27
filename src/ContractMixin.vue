<script>
export default {
    methods: {
        async getTotalSupply(contract, account) {
            return parseInt(await contract.methods.totalSupply().call({from: account}));
        },
        async ownerOf(contract, account, tokenId) {
            return await contract.methods.ownerOf(tokenId).call({from: account});
        },
        async getTokenURI(contract, account, tokenId) {
            return await contract.methods.tokenURI(tokenId).call({from: account});
        },
        async safeTransferFrom(web3, contract, account, recipientAddress, tokenId) {
            console.log('Send token', tokenId, 'to', recipientAddress);
            let gasPrice = await web3.eth.getGasPrice();
            console.log('Estimated gas price', gasPrice);
            try {
                await contract.methods.safeTransferFrom(account, recipientAddress, tokenId).send({from: account, gasPrice: gasPrice});
            } catch (error) {
                console.log('Send token was cancelled');
                return false;
            }
            console.log('Send completed');
            return true;
        },
        async getContestCurrentCountOfMembers(contract, account) {
            return parseInt(await contract.methods.currentCountOfMembers().call({from: account}));
        },
        async getContestCountOfMembersToCheck(contract, account) {
            return parseInt(await contract.methods.countOfMembersToCheck().call({from: account}));
        }
    }
}
</script>

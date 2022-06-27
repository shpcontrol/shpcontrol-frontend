<script>
export default {
    props: ['web3'],
    emits: ['close', 'send'],
    data() {
        return {
            recipientAddress: '',
            invalidAddress: false
        };
    },
    methods: {
        sendTokenToAddress() {
            if(this.web3.utils.isAddress(this.recipientAddress)) {
                this.$emit('send', this.recipientAddress);
            } else {
                this.invalidAddress = true;
            }
        }
    }
}
</script>

<template>
    <div class="modal" tabindex="-1" style="display: block">
        <div class="modal-dialog">
            <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Send</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close" @click="$emit('close')"></button>
            </div>
            <div class="modal-body">
                <div class="mb-3">
                    <label for="inputRecipientAddress" class="form-label">Recipient address</label>
                    <div class="input-group has-validation">
                        <input type="text" class="form-control" :class="{'is-invalid': invalidAddress}" id="inputRecipientAddress" v-model="recipientAddress" required>
                        <div v-if="invalidAddress" id="validationServerUsernameFeedback" class="invalid-feedback">
                            Please enter a correct ethereum address.
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-primary" @click="sendTokenToAddress">Send</button>
            </div>
            </div>
        </div>
    </div>
</template>
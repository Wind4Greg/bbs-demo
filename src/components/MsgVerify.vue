<script setup>
import { ref } from 'vue';
import { verify, messages_to_scalars, prepareGenerators, os2ip, hexToBytes, bytesToHex } from '@grottonetworking/bbs-signatures';
import IconInfo from './icons/IconInfo.vue';
import MsgVerifyInfo from './info/MsgVerifyInfo.vue';

const props = defineProps(['sigBundle']);

let sigBundleString = ref("");
let sigBundle = ref({});
let verifiedText = ref("?");

async function verifySig() {
    console.log("Verify signature called");
    try {
        // Need to convert messages to octets!
        let te = new TextEncoder();
        let messages = sigBundle.value.messages;
        let messagesOctets = messages.map(msg => te.encode(msg));
        let msg_scalars = await messages_to_scalars(messagesOctets);
        let gens = await prepareGenerators(messages.length); // Generate enough for all messages
        let headerBytes = hexToBytes(sigBundle.value.header);
        let pk_bytes = hexToBytes(sigBundle.value.publicKey);
        let signature = hexToBytes(sigBundle.value.signature);
        let verified = await verify(pk_bytes, signature, headerBytes, msg_scalars, gens);
        verifiedText.value = verified.toString();
    } catch (error) {
        console.log(`Problem with signature ${error}`);
    }
}

function processBundleText() {
    try {
        sigBundle.value = JSON.parse(sigBundleString.value);
        verifiedText.value = "?";
    } catch {
        console.log("Bad signature bundle string")
        sigBundle.value = {};
    }
}

function addMessage() {
    sigBundle.value.messages.push("");
    verifiedText.value = "?";
}

function removeMessage(i) {
    sigBundle.value.messages.splice(i, 1);
    verifiedText.value = "?";
}

function useLocalSig() {
    sigBundleString.value = props.sigBundle;
    processBundleText();
}

</script>

<template>
    <div class="card">
        <div class="card-header space-between">
            <h4>Signature Bundle Verification</h4><button type="button" class="btn text-nowrap" data-bs-toggle="modal"
                data-bs-target="#sigVerifyModal">
                <IconInfo />
            </button>
        </div>

        <div class="card-body">
            <form>
                <div class="mb-3">
                    <div class="space-between mb-1">
                        <label for="sigBundleText" class="form-label">Signature Bundle JSON</label><button type="button"
                            class="btn btn-outline-secondary btn-small" @click="useLocalSig">Use above
                            signature</button>
                    </div>
                    <textarea class="form-control mb-1" v-model="sigBundleString" id="sigBundleText"
                        rows="3"></textarea>
                    <button type="button" class="btn btn-primary" @click="processBundleText">Process JSON</button>
                </div>
            </form>

            <details class="mb-2">
                <summary>Modify Signature Bundle</summary>
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">Changing any field invalidates the signature</h5>
                        <form class="mb-3">
                            <div class="mb-3">
                                <label for="sigHeader" class="form-label">Signature Header Hex</label>
                                <input type="text" class="form-control" id="sigHeader" v-model="sigBundle.header">
                            </div>
                            <fieldset>
                                <legend>Messages:</legend>
                                <div v-for="(msg, index) in sigBundle.messages" class="mb-3 input-group">
                                    <input type="text" class="form-control" :placeholder="msg"
                                        v-model="sigBundle.messages[index]">
                                    <button class="btn btn-outline-secondary" @click="removeMessage(index)"
                                        type="button">Remove</button>
                                </div>
                                <button type="button" class="btn btn-primary" @click="addMessage">Add Message</button>
                            </fieldset>
                        </form>
                    </div>
                </div>
            </details>
            <div id="VerifySection">
                <button type="button" class="btn btn-secondary" @click="verifySig">Verify Signature</button>
                <h3>Verified: {{ verifiedText }}</h3>
            </div>



        </div>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="sigVerifyModal" tabindex="-1" aria-labelledby="sigVerifyModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-scrollable modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="sigVerifyModalLabel">Signature Verification</h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <MsgVerifyInfo />
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped>
#VerifySection {
    display: flex
}

#VerifySection button {
    margin-right: 1em
}
</style>

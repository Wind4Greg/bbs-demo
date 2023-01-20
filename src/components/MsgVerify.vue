<script setup>
import { ref } from 'vue';
import { verify, messages_to_scalars, prepareGenerators, os2ip, hexToBytes, bytesToHex } from './BBSAllinOne.js';

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
        <div class="card-header">
            Message Verification
        </div>

        <div class="card-body">
            <form>
                <div class="mb-3">
                    <label for="sigBundleText" class="form-label">Signature Bundle JSON</label><button type="button" class="btn btn-outline-secondary btn-small" @click="useLocalSig">Use above signature</button>
                    <textarea class="form-control" v-model="sigBundleString" id="sigBundleText" rows="3"></textarea>
                    <button type="button" class="btn btn-primary" @click="processBundleText">Process JSON</button>
                </div>
            </form>
            <form class="mb-3">
                <div class="mb-3">
                    <label for="sigHeader" class="form-label">Signature Header Hex</label>
                    <input type="text" class="form-control" id="sigHeader" v-model="sigBundle.header">
                </div>
                <fieldset>
                    <legend>Messages:</legend>
                    <div v-for="(msg, index) in sigBundle.messages" class="mb-3 input-group">
                        <input type="text" class="form-control" :placeholder="msg" v-model="sigBundle.messages[index]">
                        <button class="btn btn-outline-secondary" @click="removeMessage(index)"
                            type="button">Remove</button>
                    </div>
                    <button type="button" class="btn btn-primary" @click="addMessage">Add Message</button>
                </fieldset>
            </form>
            <div id="VerifySection">
                <button type="button" class="btn btn-secondary" @click="verifySig">Verify Signature</button>
                <h3>Verified: {{ verifiedText }}</h3>
            </div>



        </div>
    </div>
</template>
<style scoped>
    #VerifySection {display: flex}
    #VerifySection button {margin-right: 1em}
</style>

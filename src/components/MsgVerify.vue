<script setup>
import { ref, computed } from 'vue';
import { verify, messages_to_scalars, prepareGenerators, os2ip, hexToBytes, bytesToHex } from './BBSAllinOne.js';
import * as bls from '@noble/bls12-381';

let sigBundleString = ref("");
let sigBundle = ref({});
let verifiedText = ref("");


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
        verifiedText.value = "";
    } catch {
        console.log("Bad signature bundle string")
        sigBundle.value = {};
    }
}



</script>

<template>
    <div class="card">
        <div class="card-header">
            Message Verification
        </div>

        <div class="card-body">
            <input type="textarea" v-model="sigBundleString" />
            <button @click="processBundleText">Process JSON</button>
            <button @click="verifySig">Verify Signature</button>
            <h2>Verified: {{ verifiedText }}</h2>
        </div>
    </div>
</template>

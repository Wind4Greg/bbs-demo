<script setup>
import { ref, computed } from 'vue';
import { sign, verify, messages_to_scalars, prepareGenerators, os2ip, hexToBytes, bytesToHex } from './BBSAllinOne.js';
import * as bls from '@noble/bls12-381';

const props = defineProps(['keys'])

// TODO: export JSON string with relevant data and signature
let messages = ref(["University of Nowhere Fall 2020", "Physics 137A: A", "Guitar 101: B", "Dance Elementary: D"]);
let header = ref("11223344556677889900aabbccddeeff");
let signature = ref("");



function addMessage() {
    messages.value.push("");
}

function removeMessage(i) {
    messages.value.splice(i, 1);
}

async function createSig() {
    console.log("Create signature called");
    try {
        // Need to convert messages to octets!
        let te = new TextEncoder();
        let messagesOctets = messages.value.map(msg => te.encode(msg));
        let msg_scalars = await messages_to_scalars(messagesOctets);
        let gens = await prepareGenerators(messages.value.length); // Generate enough for all messages
        let headerBytes = hexToBytes(header.value);
        let result = await sign(props.keys.secretScalar, props.keys.publicBytes, headerBytes,
            msg_scalars, gens);
        signature.value = bytesToHex(result);
        console.log(`Finished signature computation: ${signature.value}`);
    } catch (error) {
        console.log(`Problem with signature ${error}`);
    }

}
</script>

<template>
    <div class="card">
        <div class="card-header">
            Message Signing
        </div>

        <div class="card-body">
            <form>
                <!-- <div class="mb-3">
                    <label for="secretKey" class="form-label">Secret Key Scalar</label>
                    <input type="text" class="form-control" id="secretKey" :placeholder="props.keys.secretScalar">
                </div>
                <div class="mb-3">
                    <label for="publicKey" class="form-label">Public Key</label>
                    <input type="text" class="form-control" id="publicKey" :placeholder="bytesToHex(props.keys.publicBytes)">
                </div> -->
                <div class="mb-3">
                    <label for="sigHeader" class="form-label">Signature Header Hex</label>
                    <input type="text" class="form-control" id="sigHeader" v-model="header">
                </div>
                <fieldset>
                    <legend>Messages:</legend>
                    <div v-for="(msg, index) in messages" class="mb-3 input-group">
                        <input type="text" class="form-control" :placeholder="msg" v-model="messages[index]">
                        <button class="btn btn-outline-secondary" @click="removeMessage(index)"
                            type="button">Remove</button>
                    </div>
                    <button type="button" class="btn btn-primary" @click="addMessage">Add Message</button>
                </fieldset>
                <button type="button" class="btn btn-primary" @click="createSig">Create Signature</button>
            </form>
            <div>
                <h3>Signature</h3>
                <p>{{ signature }}</p>
            </div>

        </div>
    </div>
</template>

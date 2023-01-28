<script setup>
import { ref} from 'vue';
import { sign, messages_to_scalars, prepareGenerators, hexToBytes, bytesToHex } from '@grottonetworking/bbs-signatures';

const props = defineProps(['keys'])
const emit = defineEmits(['signature']);

let messages = ref(["University of Nowhere Fall 2020", "Physics 137A: A", "Guitar 101: B", "Dance Elementary: D"]);
let header = ref("11223344556677889900aabbccddeeff");
let signature = ref("");
let signatureBundle = ref({});



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
        signatureBundle.value = { publicKey: bytesToHex(props.keys.publicBytes),
            header: header.value,
            messages: messages.value,
            signature: signature.value
        }
        console.log(`Finished signature computation: ${signature.value}`);
        let bundleString = JSON.stringify(signatureBundle.value, null, 2);
        emit("signature", bundleString);
    } catch (error) {
        console.log(`Problem with signature ${error}`);
    }
}

function copySig() {
    navigator.clipboard.writeText(JSON.stringify(signatureBundle.value, null, 2))
}
</script>

<template>
    <div class="card">
        <div class="card-header">
            Message Signing
        </div>

        <div class="card-body">
            <form>
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
                <textarea class="form-control" readonly >{{ signature }}</textarea>
                <h4>Signature Bundle <button type="button" class="btn btn-outline-secondary btn-small" @click="copySig">Copy to Clipboard</button></h4>
                <textarea class="form-control" readonly >{{ JSON.stringify(signatureBundle, null, 2) }}</textarea>

            </div>

        </div>
    </div>
</template>

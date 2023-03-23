<script setup>
import { ref } from 'vue';
import { sign, messages_to_scalars, prepareGenerators, hexToBytes, bytesToHex } from '@grottonetworking/bbs-signatures';
import IconInfo from './icons/IconInfo.vue';
import MsgSignInfo from './info/MsgSignInfo.vue';

const props = defineProps(['keys'])
const emit = defineEmits(['signature']);

let messages = ref(["FirstName: Sequoia",
    "LastName: Sempervirens",
    "Address: Jedediah Smith Redwoods State Park, California",
    "Date of Birth: 1200/03/21",
    "Height: 296 feet",
    "Eyes: None",
    "Hair: Brown bark, green needles",
    "Picture: Encoded photo",
    "License Class: None, Trees can't drive" ]);
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
        signatureBundle.value = {
            publicKey: bytesToHex(props.keys.publicBytes),
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
        <div class="card-header space-between">
            <h4>Signature Creation</h4><button type="button" class="btn text-nowrap" data-bs-toggle="modal"
                data-bs-target="#sigCreateModal">
                <IconInfo />
            </button>
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

                </fieldset>
                <div class="space-between mb-3">
                    <button type="button" class="btn btn-secondary" @click="addMessage">Add Message</button>
                    <button type="button" class="btn btn-primary" @click="createSig">Create Signature</button>
                </div>
            </form>
            <div class="card">
                <div class="card-body">
                    <h3>Signature</h3>
                    <textarea class="form-control" readonly>{{ signature }}</textarea>
                    <div class="space-between my-2">
                        <h4>Signature Bundle</h4> <button type="button" class="btn btn-outline-secondary btn-small"
                            @click="copySig">Copy to Clipboard</button>
                    </div>
                    <textarea class="form-control" readonly>{{ JSON.stringify(signatureBundle, null, 2) }}</textarea>
                </div>
            </div>

        </div>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="sigCreateModal" tabindex="-1" aria-labelledby="sigCreateModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-scrollable modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="sigCreateModalLabel">Signature Creation Info</h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <MsgSignInfo />
                </div>
            </div>
        </div>
    </div>
</template>

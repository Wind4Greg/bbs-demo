<script setup>
import { ref } from 'vue';
import { proofGen, messages_to_scalars, prepareGenerators, hexToBytes, bytesToHex } from '@grottonetworking/bbs-signatures';
import IconInfo from './icons/IconInfo.vue';
import ProofGenInfo from './info/ProofGenInfo.vue';

const props = defineProps(['sigBundle']);
const emit = defineEmits(['proof']);

let sigBundleString = ref("");
let sigBundle = ref({});
let disclosed = ref([]);
let proofBundleText = ref("");
let proofHex = ref("");


async function genProof() {
    console.log("Generate Proof called");
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
        let ph = new Uint8Array(); // Empty proof header for now
        let result = await proofGen(pk_bytes, signature, headerBytes, ph, msg_scalars, disclosed.value, gens);
        let disclosedMsgs = messages.filter((msg, i) => disclosed.value.includes(i));
        proofHex.value = bytesToHex(result);
        let proofBundle = {
            pk: bytesToHex(pk_bytes),
            header: bytesToHex(headerBytes),
            ph: bytesToHex(ph),
            disclosedIndexes: disclosed.value,
            disclosedMsgs: disclosedMsgs,
            totalMsgs: messages.length,
            proof: bytesToHex(result)
        }
        proofBundleText.value = JSON.stringify(proofBundle, null, 2);
        emit("proof", proofBundleText.value);
    } catch (error) {
        console.log(`Problem with signature ${error}`);
    }
}

function processSigBundleText() {
    try {
        sigBundle.value = JSON.parse(sigBundleString.value);
    } catch {
        console.log("Bad signature bundle string")
        sigBundle.value = {};
    }
}

function addMessage() {
    sigBundle.value.messages.push("");
}

function disclosedCheck(i) { // Keeps track of disclosed indices
    // console.log(`Disclosed check: i = ${i}`);
    let index = disclosed.value.indexOf(i);
    if (index == -1) { // Add to disclosed indexes
        disclosed.value.push(i);
        disclosed.value.sort();
    } else { // Remove from disclosed indices
        disclosed.value.splice(index, 1);
    }
}

function copyProof() {
    navigator.clipboard.writeText(proofBundleText.value);
}

function useLocalSig() {
    sigBundleString.value = props.sigBundle;
    processSigBundleText();
}

</script>

<template>
    <div class="card">
        <div class="card-header space-between">
            <h4>Proof Generation</h4><button type="button" class="btn text-nowrap" data-bs-toggle="modal"
                data-bs-target="#proofGenModal">
                <IconInfo />
            </button>
        </div>

        <div class="card-body">
            <form>
                <div class="mb-3">
                    <div class="space-between mb-1">
                        <label for="sigBundleText" class="form-label">Signature Bundle JSON</label>
                        <button type="button" class="btn btn-outline-secondary btn-small" @click="useLocalSig">Use above
                            signature</button>
                    </div>
                    <textarea class="form-control mb-1" v-model="sigBundleString" id="sigBundleText" rows="3"></textarea>
                    <button type="button" class="btn btn-primary" @click="processSigBundleText">Process JSON</button>
                </div>
            </form>
            <form class="mb-3">
                <fieldset>
                    <legend>Messages (select to include):</legend>
                    <div v-for="(msg, index) in sigBundle.messages" class="mb-3 input-group">
                        <input type="text" class="form-control" :placeholder="msg" v-model="sigBundle.messages[index]">
                        <span class="input-group-text">
                            <input class="form-check-input" type="checkbox" @change="disclosedCheck(index)">
                        </span>
                    </div>
                </fieldset>
                <button type="button" class="btn btn-secondary" @click="genProof">Generate Proof</button>
            </form>
            
            <div class="card">
                <div class="card-body">
                    <h3>Proof</h3>
                    <textarea class="form-control" readonly>{{ proofHex }}</textarea>
                    <div class="space-between my-2">
                        <h4>Proof Bundle</h4> <button type="button" class="btn btn-outline-secondary btn-small"
                            @click="copyProof">Copy to Clipboard</button>
                    </div>
                    <textarea class="form-control" readonly>{{ proofBundleText }}</textarea>
                </div>
            </div>
        </div>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="proofGenModal" tabindex="-1" aria-labelledby="proofGenModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-scrollable modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="proofGenModalLabel">Proof Generation</h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <ProofGenInfo />
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped>

</style>

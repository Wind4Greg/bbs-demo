<script setup>
import { ref} from 'vue';
import { proofVerify, messages_to_scalars, prepareGenerators, hexToBytes, bytesToHex } from './BBSAllinOne.js';

let proofBundleText = ref("");
let proofBundle = ref({});
let verifiedText = ref("?");


async function verifyProof() {
    // TODO: convert to verify
    console.log("Verify Proof called");
            // let proofBundle = {
        //     pk: bytesToHex(pk_bytes),
        //     header: bytesToHex(headerBytes),
        //     ph: bytesToHex(ph),
        //     disclosedIndexes: disclosed.value,
        //     disclosedMsgs: disclosedMsgs,
        //     totalMsgs: messages.length,
        //     proof: bytesToHex(result)
        // }
    try {
        // Need to convert messages to octets!
        let te = new TextEncoder();
        let disclosedMsgs = proofBundle.value.disclosedMsgs;
        let disclosedMsgsOctets = disclosedMsgs.map(msg => te.encode(msg));
        let disclosedMsgScalars = await messages_to_scalars(disclosedMsgsOctets);
        let L = proofBundle.value.totalMsgs;
        let gens = await prepareGenerators(L); // Generate enough for all messages
        let headerBytes = hexToBytes(proofBundle.value.header);
        let pk_bytes = hexToBytes(proofBundle.value.pk);
        let proof = hexToBytes(proofBundle.value.proof);
        let ph = hexToBytes(proofBundle.value.ph); // Empty proof header for now
        let result = await proofVerify(pk_bytes, proof, L, headerBytes, ph, disclosedMsgScalars, 
            proofBundle.value.disclosedIndexes, gens);
        verifiedText.value = result.toString();

    } catch (error) {
        console.log(`Problem with proof verify ${error}`);
    }
}

function processProofBundleText() {
    try {
        proofBundle.value = JSON.parse(proofBundleText.value);
    } catch {
        console.log("Bad proof bundle text")
        proofBundle.value = {};
    }
    verifiedText.value = "?";
}


</script>

<template>
    <div class="card">
        <div class="card-header">
            Proof Verification
        </div>

        <div class="card-body">
            <form>
                <div class="mb-3">
                    <label for="sigBundleText" class="form-label">Proof Bundle JSON</label>
                    <textarea class="form-control" v-model="proofBundleText" id="sigBundleText" rows="3"></textarea>
                    <button type="button" class="btn btn-primary" @click="processProofBundleText">Process JSON</button>
                </div>
            </form>

            <div id="VerifySection">
                <button type="button" class="btn btn-secondary" @click="verifyProof">Verify Proof</button>
                <h3>Verified = {{ verifiedText }}</h3>
            </div>
        </div>
    </div>
</template>
<style scoped>
</style>

<script setup>
import { ref } from 'vue';
import { proofVerify, messages_to_scalars, prepareGenerators, hexToBytes, numUndisclosed } from '@grottonetworking/bbs-signatures';
import IconInfo from './icons/IconInfo.vue';
import ProofVerifyInfo from './info/ProofVerifyInfo.vue';

const props = defineProps(['proofBundleString']);

let proofBundleText = ref("");
let proofBundle = ref({});
let verifiedText = ref("?");


async function verifyProof() {
    console.log("Verify Proof called");
    try {
        // Need to convert messages to octets!
        let te = new TextEncoder();
        let disclosedMsgs = proofBundle.value.disclosedMsgs;
        let disclosedMsgsOctets = disclosedMsgs.map(msg => te.encode(msg));
        let disclosedMsgScalars = await messages_to_scalars(disclosedMsgsOctets);
        let proof = hexToBytes(proofBundle.value.proof);
        let L = numUndisclosed(proof) + disclosedMsgScalars.length;
        let gens = await prepareGenerators(L); // Generate enough for all messages
        let headerBytes = hexToBytes(proofBundle.value.header);
        let pk_bytes = hexToBytes(proofBundle.value.pk);
    
        let ph = hexToBytes(proofBundle.value.ph); // Empty proof header for now
        let result = await proofVerify(pk_bytes, proof, headerBytes, ph, disclosedMsgScalars,
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

function useLocalProof() {
    proofBundleText.value = props.proofBundleString;
    processProofBundleText();
}


</script>

<template>
    <div class="card">
        <div class="card-header space-between">
            <h4>Proof Verification</h4><button type="button" class="btn text-nowrap" data-bs-toggle="modal"
                data-bs-target="#proofVerifyModal">
                <IconInfo />
            </button>
        </div>

        <div class="card-body">
            <form>
                <div class="mb-3">
                    <div class="space-between mb-1">
                        <label for="sigBundleText" class="form-label">Proof Bundle JSON</label>
                        <button type="button" class="btn btn-outline-secondary btn-small" @click="useLocalProof">Use
                            above
                            proof</button>
                    </div>
                    <textarea class="form-control mb-1" v-model="proofBundleText" id="sigBundleText" rows="3"></textarea>
                    <button type="button" class="btn btn-primary" @click="processProofBundleText">Process JSON</button>
                </div>
            </form>

            <div id="VerifySection">
                <button type="button" class="btn btn-secondary" @click="verifyProof">Verify Proof</button>
                <h3>Verified = {{ verifiedText }}</h3>
            </div>
        </div>
    </div>
        <!-- Modal -->
        <div class="modal fade" id="proofVerifyModal" tabindex="-1" aria-labelledby="proofVerifyModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-scrollable modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="proofVerifyModalLabel">Proof Verification</h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <ProofVerifyInfo />
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

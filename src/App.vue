<script setup>
import {ref} from "vue";
import KeyGeneration from './components/KeyGeneration.vue';
import MsgSign from './components/MsgSign.vue';
import MsgVerify from "./components/MsgVerify.vue";
import ProofGen from "./components/ProofGen.vue";
import ProofVerify from "./components/ProofVerify.vue";

let keys = ref({});
let sigBundle = ref("");
let proofBundleText = ref("");

function keyUpdate(keyInfo) {
  keys.value = keyInfo;
}

function sigUpdate(sigText) {
  sigBundle.value = sigText;
}

function proofUpdate(proofText) {
  proofBundleText.value = proofText;
}

</script>

<template>
  <main>
    <h1>BBS Signature Demo</h1>
    <KeyGeneration @keys="keyUpdate"/>
    <MsgSign :keys="keys" @signature="sigUpdate"/>
    <MsgVerify :sigBundle="sigBundle"/>
    <ProofGen :sigBundle="sigBundle" @proof="proofUpdate" />
    <ProofVerify :proofBundleString="proofBundleText" />
  </main>
</template>

<style scoped>
</style>

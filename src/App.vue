<script setup>
import { ref } from "vue";
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
    <div class="row row-cols-1 row-cols-md-2 g-4">
      <div class="col">
        <KeyGeneration @keys="keyUpdate" />
      </div>
      <div class="col">
        <MsgSign class="col" :keys="keys" @signature="sigUpdate" />
      </div>
      <div class="col">
        <MsgVerify :sigBundle="sigBundle" />
      </div>
      <div class="col">
        <ProofGen :sigBundle="sigBundle" @proof="proofUpdate" />
      </div>
      <div class="col">
        <ProofVerify :proofBundleString="proofBundleText" />
      </div>
    </div>
  </main>
</template>

<style>
.card-header.greg {
  display: flex;
  justify-content: space-between;
}
</style>

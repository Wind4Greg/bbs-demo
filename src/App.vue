<script setup>
import { ref } from "vue";
import KeyGeneration from './components/KeyGeneration.vue';
import MsgSign from './components/MsgSign.vue';
import MsgVerify from "./components/MsgVerify.vue";
import ProofGen from "./components/ProofGen.vue";
import ProofVerify from "./components/ProofVerify.vue";
import IconInfo from './components/icons/IconInfo.vue';
import AppInfo from './components/info/AppInfo.vue'
import {Modal} from 'bootstrap';

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
    <h1>BBS Signature Demo <button type="button" class="btn text-nowrap" data-bs-toggle="modal"
        data-bs-target="#appInfoModal">
        <IconInfo />
      </button></h1>
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

  <!-- Modal -->
  <div class="modal fade" id="appInfoModal" tabindex="-1" aria-labelledby="appInfoModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="appInfoModalLabel">About BBS Signatures</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <AppInfo />
        </div>
      </div>
    </div>
  </div>

</template>

<style>
.space-between {
  display: flex;
  justify-content: space-between;
}
</style>

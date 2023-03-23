<script setup>
import { ref, computed } from 'vue';
import { os2ip, hexToBytes, bytesToHex, publicFromPrivate, keyGen } from '@grottonetworking/bbs-signatures';
import IconInfo from './icons/IconInfo.vue';
import KeyGenInfo from './info/KeyGenInfo.vue';

const emit = defineEmits(['keys']);

let secretKeyHex = ref("47d2ede63ab4c329092b342ab526b1079dbc2595897d4f2ab2de4d841cbe7d56");
let validSecretHex = computed(() => {
  if (secretKeyHex.value.length !== 64) {
    return false;
  }
  try {
    let sk_bytes = hexToBytes(secretKeyHex.value); // Will check if valid hex bytes for us
    return true;
  } catch (e) {
    return false;
  }
})

let pk_bytes = computed(() => {
  try {
    let sk_bytes = hexToBytes(secretKeyHex.value);
    console.log(secretKeyHex.value);
    let publicBytes = publicFromPrivate(sk_bytes);
    emit("keys", { secretScalar: os2ip(sk_bytes), publicBytes: publicBytes })
    return publicBytes;
  } catch {
    return new Uint8Array();
  }
})

async function generateRandomSecret() {
  let keyMaterial = window.crypto.getRandomValues(new Uint8Array(32));
  const keyInfo = new TextEncoder().encode("BBS-Demo Key Generator");
  let sk = await keyGen(keyMaterial, keyInfo);
  secretKeyHex.value = bytesToHex(sk);
}

</script>

<template>
  <div class="card">
    <div class="card-header space-between">
      <h4>Key Generation</h4><button type="button" class="btn text-nowrap" data-bs-toggle="modal"
        data-bs-target="#keyGenModal">
        <IconInfo />
      </button>
    </div>

    <div class="card-body">
      <form>
        <p>Private Key in Hex <button type="button" class="btn btn-primary btn-sm"
            @click="generateRandomSecret">Generate Random Key</button></p>
        <input type="text" class="form-control" v-model="secretKeyHex" />
        <p>Private Key as Scalar:</p>
        <textarea class="form-control" readonly>{{ validSecretHex? os2ip(hexToBytes(secretKeyHex)) : "" }}</textarea>
        <p>Public Key in Hex</p>
        <textarea class="form-control" readonly>{{ bytesToHex(pk_bytes) }}</textarea>
      </form>

    </div>
  </div>
  <!-- Modal -->
  <div class="modal fade" id="keyGenModal" tabindex="-1" aria-labelledby="keyGenModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-scrollable modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="keyGenModalLabel">Key Generation Info</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <KeyGenInfo />
        </div>
      </div>
    </div>
  </div>
</template>

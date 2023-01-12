<script setup>
import { ref, computed } from 'vue';
import { os2ip, hexToBytes, bytesToHex } from './BBSAllinOne.js';
import * as bls from '@noble/bls12-381';

const emit = defineEmits(['keys']);

let secretKeyHex = ref("47d2ede63ab4c329092b342ab526b1079dbc2595897d4f2ab2de4d841cbe7d56");
// let sk_bytes = hexToBytes(secretKeyHex.value);
// let pointPk = bls.PointG2.fromPrivateKey(sk_bytes);
// let pk_bytes = pointPk.toRawBytes(true);
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
    let pointPk = bls.PointG2.fromPrivateKey(sk_bytes);
    let publicBytes = pointPk.toRawBytes(true);
    emit("keys", {secretScalar: os2ip(sk_bytes), publicBytes: publicBytes})
    return publicBytes;
  } catch {
    return new Uint8Array();
  }
})

function generateRandomSecret() {
  let keyBytes = window.crypto.getRandomValues(new Uint8Array(32));
  secretKeyHex.value = bytesToHex(keyBytes);
}

</script>

<template>
  <div class="card">
    <div class="card-header">
      Key Generation
    </div>

    <div class="card-body">
      <form>
        <p>Private Key in Hex <button type="button" class="btn btn-primary btn-sm" @click="generateRandomSecret">Generate Random Key</button></p>
        <input type="text" class="form-control" v-model="secretKeyHex" />
        
        <p>Private Key as Scalar: {{ validSecretHex? os2ip(hexToBytes(secretKeyHex)) : "" }}</p>
        <p>Public Key in Hex</p>
        <p>{{ bytesToHex(pk_bytes) }}</p>
      </form>

    </div>
  </div>
</template>

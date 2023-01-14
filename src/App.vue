<script setup>
import {ref} from "vue";
import KeyGeneration from './components/KeyGeneration.vue';
import MsgSign from './components/MsgSign.vue';
import MsgVerify from "./components/MsgVerify.vue";
import ProofGen from "./components/ProofGen.vue";

let keys = ref({});

function keyUpdate(keyInfo) {
  console.log(keyInfo);
  keys.value = keyInfo;
}
</script>

<template>
  <main>
    <h1>BBS Signature Demo</h1>
    <KeyGeneration @keys="keyUpdate"/>
    <MsgSign :keys="keys"/>
    <MsgVerify />
    <ProofGen />
  </main>
</template>

<style scoped>
</style>

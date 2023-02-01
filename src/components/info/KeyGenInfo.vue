<script setup>
</script>
<template>
    <p>To sign documents you need to have a private key. For our BBS signatures
        this key <strong>must</strong> be 64 hexadecimal characters (0-9, a, b, c, d, e, or, f). You
        can click the <em>Generate Random Key</em> button and we will generate an appropriate key for you
        or you can enter exactly 64 hexadecimal characters.</p>
    <p>After you have entered a valid private key we will show you its value as a very large
        integer in the <em>Private Key as Scalar</em> field. In addition we will show you the corresponding
        <em>Public Key in Hex</em>.
    </p>
    <p>The private key is used during document signing and the public key is included in our
        &quot;signature bundle&quot; to enable verification of the signature.</p>
</template>
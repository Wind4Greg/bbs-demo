<script setup>
</script>
<template>
    <p>Here you create a document that consists of multiple <em>messages</em> to
        be signed. I've furnished a few sample messages that you can modify or
        remove and you can add as many new messages as you like.</p>
    <p>The signing algorithm takes an optional <em>Signature Header</em> which <strong>must</strong> be an
        even number of hexadecimal characters (a valid array of bytes). You can modify the default
        or leave it alone.</p>
    <p>Clicking <em>Create Signature</em> creates the cryptographic signature which is shown in hex and
        also creates a <em>Signature Bundle</em> which includes all the information needed for signature
        verification and coming up with &quot;derived proofs&quot;, i.e., documents that contain only a subset
        of the original document messages. You can copy this information to the clipboard
        and save it for later use if you like.</p>
</template>
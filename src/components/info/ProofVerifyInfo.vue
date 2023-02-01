<script setup>
</script>
<template>
    <p>The proof verify step allows one to verify the proof. What this means is that:</p>
    <ol>
        <li>The disclosed messages can only have come from the original signed document</li>
        <li>The disclosed messages have not been modified.</li>
        <li>The verification is performed against the original signers public key.</li>
    </ol>
    <p>You can either click on the <em>Use above proof</em> button or paste a JSON &quot;proof bundle&quot; into
        the text area and click the <em>Process JSON</em> button.</p>
    <p>Clicking the <em>Verify Proof</em> button with run the verification algorithm and tell you the result.</p>
</template>
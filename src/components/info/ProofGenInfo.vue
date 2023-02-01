<script setup>
</script>
<template>
    <p><em>Proof Generation</em> is where BBS signatures have an important additional feature over
        other signature schemes. This step allows the recipient of the signed document to
        &quot;selectively&quot; disclose a subset of the original messages within the signed document.</p>
    <p>You can either click the <em>Use above signature</em> button or paste a JSON signature bundle
        into the text area and hit the <em>Process JSON</em> button.</p>
    <p>Then you can click on the check boxes next to an individual message to include it in the
        <em>proof</em>. <em><strong>Note</strong></em>: Changing the contents of the message will result in the
        generated proof
        <strong>not</strong> verifying.
    </p>
    <p>When you click the <em>Generate Proof</em> button it will generate a <em><strong>new</strong></em> cryptographic
        proof value
        and a <em>Proof Bundle</em> which can be shared and verified. <em><strong>Note</strong></em>: each time you
        click the <em>Generate Proof</em> button a new cryptographically distinct proof value is generated. This is the
        important <strong>unlinkable</strong> property of BBS proofs.</p>

</template>
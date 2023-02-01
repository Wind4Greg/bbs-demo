<script setup>
</script>
<template>
    <p>This in browser application demonstrates the capabilities of BBS signatures, an advanced digital
        signature scheme currently under standardization with proven cryptographic properties.
        <strong>Not</strong> intended for any purposed other than algorithm demonstration!
    </p>
    <p>There are several key sections to the application:</p>
    <ul>
        <li><strong>Key Generation</strong>: This is where the &quot;original&quot; signer of the &quot;document&quot;
            creates or inputs their secret key and the application creates the corresponding public key used in
            <em>signature</em> and <em>proof</em> <strong>verification</strong>.</li>
        <li><strong>Document creation and Signing</strong>: BBS signatures apply to a &quot;document&quot; that can
            contain multiple independent messages. A key feature of these signatures is that later on only a subset of
            these messages need to be revealed in the &quot;proof process&quot; and can still be verified. This section
            of the app allows you to add/remove/modify messages and then produce a signature.</li>
        <li><strong>Document Verification</strong>: Given a signed &quot;document&quot; containing multiple messages you
            can verify that the individual messages included have not been modified. This section of the app allows you
            to verify the signature, but also allows you to try modifying messages and see that the signature no longer
            validates.</li>
        <li><strong>Proof Generation</strong>: BBS signatures let the recipient of the &quot;signature bundle&quot; to
            produce &quot;proofs&quot;, i.e., new documents with only a subset of the original messages (called
            <em>selective disclosure</em>). In addition, these &quot;proofs&quot; allow detection of modification of the
            signatures against the original public key, but these &quot;proofs&quot; are <em>unlinked</em> from the
            original signature and each other. This section allows you to select messages to include in such proofs and
            lets you attempt to modify individual messages.</li>
        <li><strong>Proof</strong> Verification: This section of the application allows you to verify a proof. Note that
            if you tried to change a message in the <em>proof generation</em> step the proof will not validate.</li>
    </ul>
    <p><strong>Questions or Comments</strong>? Email: <a
            href="mailto:gregb@grotto-networking.com">gregb@grotto-networking.com</a></p>
</template>
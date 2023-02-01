This in browser application demonstrates the capabilities of BBS signatures, an advanced digital
signature scheme currently under standardization with proven cryptographic properties. 
**Not** intended for any purposed other than algorithm demonstration!

There are several key sections to the application:

* **Key Generation**: This is where the "original" signer of the "document" creates or inputs their secret key and the application creates the corresponding public key used in *signature* and *proof* **verification**.
* **Document creation and Signing**: BBS signatures apply to a "document" that can contain multiple independent messages. A key feature of these signatures is that later on only a subset of these messages need to be revealed in the "proof process" and can still be verified. This section of the app allows you to add/remove/modify messages and then produce a signature.
* **Document Verification**: Given a signed "document" containing multiple messages you can verify that the individual messages included have not been modified. This section of the app allows you to verify the signature, but also allows you to try modifying messages and see that the signature no longer validates.
* **Proof Generation**: BBS signatures let the recipient of the "signature bundle" to produce "proofs", i.e., new documents with only a subset of the original messages (called *selective disclosure*). In addition, these "proofs" allow detection of modification of the signatures against the original public key, but these "proofs" are *unlinked* from the original signature and each other. This section allows you to select messages to include in such proofs and lets you attempt to modify individual messages.
* **Proof** Verification: This section of the application allows you to verify a proof. Note that if you tried to change a message in the *proof generation* step the proof will not validate.

**Questions or Comments**? Email: gregb@grotto-networking.com
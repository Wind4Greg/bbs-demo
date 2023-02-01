*Proof Generation* is where BBS signatures have an important additional feature over
other signature schemes. This step allows the recipient of the signed document to
"selectively" disclose a subset of the original messages within the signed document.

You can either click the *Use above signature* button or paste a JSON signature bundle
into the text area and hit the *Process JSON* button.

Then you can click on the check boxes next to an individual message to include it in the
*proof*. ***Note***: Changing the contents of the message will result in the generated proof
**not** verifying.

When you click the *Generate Proof* button it will generate a ***new*** cryptographic proof value
and a *Proof Bundle* which can be shared and verified. ***Note***: each time you click the *Generate Proof* button a new cryptographically distinct proof value is generated. This is the important **unlinkable** property of BBS proofs.

Here you create a document that consists of multiple *messages* to
be signed. I've furnished a few sample messages that you can modify or
remove and you can add as many new messages as you like.

The signing algorithm takes an optional *Signature Header* which **must** be an 
even number of hexadecimal characters (a valid array of bytes). You can modify the default
or leave it alone.

Clicking *Create Signature* creates the cryptographic signature which is shown in hex and
also creates a *Signature Bundle* which includes all the information needed for signature
verification and coming up with "derived proofs", i.e., documents that contain only a subset
of the original document messages. You can copy this information to the clipboard
and save it for later use if you like.

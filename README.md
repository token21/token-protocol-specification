Abstract
========
This document describes a protocol to manage digital assets (tokens) on top of the bitcoin blockchain. The protocol enables a semantic layer that permits reading the bitcoin transactions as operations related to tokens.

The protocol allows a new level of plausible deniability, while permitting statefull public auditability on each issued token. It allows both the user and the issuer to deny that an existing bitcoin transaction between the two is actually a token transaction, or a new token issuance. While both the token sender and the token issuer cannot deny to have sent bitcoins, nobody can prove the transaction was related to a digital asset. On top of that, to guarantee plausible deniability, tokens can be issued, sent, and received using any existing bitcoin client software.

There is no need to have a wallet exclusively dedicated to manage the tokens. With a few simple precautions by the user, tokens can be managed using any existing Bitcoin wallet, while it is used for normal bitcoin transactions as well.

Since it is possible to infinitely split a token in parts, there is no definition of the number of decimals of token generated and transferred. The number of tokens is always an integer.

Every operation of the protocol is performed with Bitcoin transactions, without the use of OP_RETURN and without any form of pollution of the blockchain, or of the UTXO set.

The protocol permits atomic buy and sell transactions between tokens and Bitcoin, and between different types of tokens. The only operations that require a coin selection enabled wallet are the split and join special operations and the token offering issuance operations. Those are used to modify the token unit of measure and to receive bitcoins from third parties during a token offering issuance.

Copyright
=========
This document is licensed under the 2-clause BSD license.

Specification
=============
The full protocol specification is in the file protocol.md

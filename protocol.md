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

Motivation
==========
The current protocols that permit to issue tokens based on the bitcoin blockchain (i.e. Counterparty, Omni, Colored Coins, Coinprism, Colu) are flawed.

The existing solutions usually need dedicated wallets and/or verification nodes. Usually, a "pivot" currency is involved and atomic transactions are not permitted unless they use the pivot currency. Those protocols pollute the blockchain (30% or more) and in some cases they do not accept P2SH scripts. Since the use of a dedicated wallet is required, the users cannot plausibly deny they have got tokens. Plausible deniability on the issuer side is not available either. None of these protocols permits infinite division of the tokens, so usually the number of decimals has to be specified at issuance time. The automatic token offering issuance is not enabled as well.

Rationale
=========
Let's take an example from the real world, a yacht. We write on the yacht's license that the owner is any person that can show a one dollar bill having the serial number F82119977F. Thus the one dollar bill can be exchanged between owners with extreme simplicity and full plausible deniability. The US government will guarantee that there is no other person having the same dollar bill.

The protocol permits managing a token in the same way. The underlying Bitcoin protocol will guarantee against double spending.

Features:

 - Easy of use. Tokens can be managed using any wallet. Even if the wallet has no coin selection feature.
 - Plausible Deniability by the issuer. The issuer can generate a new type of token and nobody analyzing the blockchain will understand that the transaction is issuing a token. Even if a token is known, the issuer can issue other tokens. Since a single output contains a large number of different token types, the issuer is actually generating different types of tokens every time she sends a new Bitcoin transaction to the network.
 - Plausible Deniability by the user (no use of tokens at all, or use of a different token type). A transaction that sends tokens from Alice to Bob is a normal transaction. Nobody can understand that this transaction is moving tokens unless they explicitly know which transaction is the token issuance. In fact a single address contains a large number of token types, and the use of tokens itself can be denied.
 - Accountability. Everybody can see the state of the distribution of a type of token.
 - Tunnel mode (confidentiality by issuer and user versus a third party). Alice can send tokens to Bob and ask him to give the tokens to Charlie, without telling to Bob what is the type of the token given. Alice can disclose this information in the future, if she wants.
 - It is possible to perform open or closed issuances. While an open issuance permits to continue the issuance of tokens in the future, closed issuance guarantees that no other token of the same type will ever be issued.
 - The power to continue the issuance of an open token can be sent to another address, using a transaction. Once the power to continue the issuance is sent to someone, the former issuer cannot issue any more tokens.
 - The power to continue the issuance has the same features of plausible deniability of the possess of a token.
 - Since a token type is uniquely identified by a transaction hash, or, in some cases, by a Bitcoin address, a user can prove to be the issuer by signing a message using the Bitcoin protocol.
 - Future proof. Tokens can move following P2PKH, P2SH, P2SH-P2WPKH outputs or any other type of script
 - Blockchain pollution of the protocol transactions is almost zero. There is no OP_RETURN involved, nor any other type of "fake" addresses that pollute the UTXO database.
 - The protocol is based on the Bitcoin blockchain, but, with small changes, can be considered blockchain agnostic.
 - Atomic transactions between tokens and Bitcoin are possible.
 - Atomic transactions between different types of tokens are possible.
 - Tokens of different types can be held by the same address and by the same output.
 - Tokens can be divided indefintely, thus having any number of decimals.
 - Tokens can be issued automatically on the receiving of bitcoins. This operation performs a token offering issuance (also known as Initial Coin Offering).

Introduction
============

Where are the tokens?
---------------------
As with bitcoins, tokens are contained in unspent Bitcoin outputs. In some cases, defined below, the last five digits of the satoshi value sent to the output represent the number of tokens contained in the output.

When an output is spent, the tokens contained in the output are fully spent in the same transaction. There are no tokens outside of the tokens contained in the UTXO database.

Token issuance
--------------
The large majority of bitcoin transactions can be semantically seen as token issuances. There are two types of token issuances: closed and opened. A closed token issuance guarantees that no other token of the same type will ever be issued.

Issuance chains
---------------
An open issuance gives to one, or more, of its output the power to continue the issuance of tokens of the same type. We define such a power as Power of Continuation (POC). The transaction that will spend the output appointed with the POC will be a continuation of the same issuance chain.

Every transaction of the chain will issue the same type of token. On top of that, every transaction that is part of the chain, can also be seen as as issuance of tokens of its, new, type. A chain will be closed by a transaction having more than one output and the first output with five zeros as the last five digits of the satoshis value. No other transactions can send tokens of the same type after the close of the issuance.

Token names
-----------
A token type can have multiple names. The default name is the hash of the first transaction that issued the token.

i.e: 68330b6ab26e44f9c3e515f04d15ffe6547f29e60b809a47e50d9abf59045c1e

As alternative names, a token type can be named after the bitcoin address of one of the outputs of the transaction that first issued the token, provided the fact that the address has never been used before in the blockchain.

Note: it is better to use one of the alternate names in cases when transaction malleability is a concern.

Vanity token names
------------------
A token can be identified using only the first characters of the Bitcoin address, as alternate name defined above, if the characters are different from every previous Bitcoin address seen in the blockchain. An example is provided below.

Tokens can coexist
-------------------
Token of different types can coexist in a single output while remaining of different types. Thus a bitcoin address (actually an output of the UTXO database) can hold tokens of different types. Every Bitcoin address contains a lot of types of tokens, so that a user usually does not know all the type of tokens contained in an address.

A single transaction can send a type of token to some of the outputs while sending another type of token to a different set of its outputs. Tokens are never burned or deleted.

Use the protocol
================
This section explains a basic use case. In all the examples provided, we do not consider the fee. We assume that there is another input, not listed, that pays the transaction fee.

Alice, Bob, Charlie, and Daniel decides that they want to start a new company. Each of them will give to the new company some time, money, furniture, knowledge. They decide everyone contributed to the company with a percentage of value as follows: Alice - 40%, Bob - 12%, Charlie - 34% and Daniel - 14%. They decide that the shares of the new company can be freely resold to others and that they will accept that the annual meeting will consent vote through messages signed using the Bitcoin protocol by the owners of the shares.

Issue tokens
------------
Alice asks Bob, Charlie, and Daniel to send her 1 Bitcoin each. She asks each of them to give her a bitcoin address where they want to receive back the bitcoins along with the tokens.

She asks Charlie to generate a vanity address that has never been used before of type 1CompanyXWXjLgud9jxwxm34u.... Since there has been a previous address in the blockchain having 1Companx as the first characters, but this is the first address seen in the blockchain that has 1Company as the first characters, they will call the token with the name 1Company. This step is optional.

Then she sends, from her wallet, a transaction having the following outputs:

 - 1.00000040 to an address controlled by Alice
 - 1.00000012 to an address controlled by Bob
 - 1.00000034 to the vanity address 1CompanyXWXjLgud9jxwxm34u... controlled by Charlie
 - 1.00000014 to an address controlled by Daniel
 - 3.45322112 is the change generated by Alice's wallet

This transaction gives 40, 12, 34, 14 tokens to each one. The newly generated token type can be named after the transaction hash, or after the vanity address (optional), or after one of the addresses of the persons involved, provided that the address has never been used before.

The issuance is still open. Since they do not want to issue more shares, they decide to close the issuance (on the other side, they could decide to leave the issuance open and to hold the issuing key somewhere, or to have a multisignature address and to give the keys to the directors of the company). In order to close the issuance, Alice generates the following transaction that sends bitcoins from her wallet to addresses of her same wallet, using the change output of the previous transaction as an input:

 - 0.45000000 to an address of her wallet
 - 3.00322112 change generated by the wallet

This closes the issuance.

Send tokens
--------------
After some while, Bob decides to give some shares of the company to his husband Giacomo. He generates a new transaction spending the output of the issuance transaction:

 - 0.03400008 to Giacomo
 - 0.96600004 change generated by Bob's wallet

This transaction gives to Giacomo 8 shares of the company.

Atomic transactions
-------------------
Daniel wants to sell 3 of his 14 shares to Frank. They negotiate a price of 0.00323200 bitcoin per share. This is a total of 0.00969600 bitcoin to buy the three shares. They do not know each other very well, so they decide to make an atomic transaction that will give 0.00969600 bitcoins to Daniel and 3 shares to Frank. Daniel set an input of the new transaction with his issuance transaction output. Frank put in another input of 1.23242454 bitcoins from his wallet. The outputs of the transaction are as follows:

 - 0.22400003 to an address controlled by Frank (this gives the 3 shares to Frank)
 - 0.23200000 to an address controlled by Daniel (this is part of the payment to Daniel)
 - 0.77769614 to an address controlled by Daniel (this can be considered the change of the original issuance output of 1.00000014)
 - 0.99872851 to an address controlled by Frank (change to Frank)

Daniel sent to the inputs of the transaction 1.00000014 bitcoins and receives back 1.00969614. This gives to Daniel the 0.00969600 paid by Frank. On the other side, Frank sends 1.23242454 as an input of the transaction and receives back 1.22272854 bitcoins, thus paying exactly the 0.00969600 that needs to be paid to Daniel. This transaction sends 3 tokens from Daniel to Frank. Another 11 tokens are the tokens that are given as a change to Daniel, along with 0.23200000 bitcoins.

Specification
=============

Definitions
-----------
In order to evaluate a transaction, the outputs are sorted by the satoshis value. Once sorted, we define a "cut" output the first output having five zeros as the last five digits of the satoshi value (satoshis modulo 10^5 == 0). In the following, "first", "second", "last" are all referred to the sorted outputs.

We define as "signal" of an output the value of satoshis of the output modulo 10^5. This is the last five digits of the value, as expressed in satoshis.

Despite not mandatory, we sometimes call "c", or "change", the output having the biggest value in Satoshi. This is the last output, as sorted above. Such behavior follows the "Guidelines" section, explained below.

We use n=0 related to a sequence a1, ..., an, to indicate that there are no elements in the sequence.

Issuance of a token
-------------------
A transaction that has only one output, or has the first output that is a cut, issues no token. Every other Bitcoin transaction is an issuance of tokens of the type of the transaction.

When a issuance is open, Power of Continuation (POC), will be given to an output that will be spent in a transaction that continues the issuance of the same type of tokens.

As for the protocol behavior, we divide the structure of the sorted outputs of a bitcoin transaction in the following groups. For each group, a description of the behavior of the protocol is provided.

 - a1, ..., an, cut(POC), z1(POC), ... zm(POC), b1, ..., bl, with n>0, m>=0, m+l>0 
     * zi are outputs signaling zero. They are optional.
     * This is an open issuance. It generates the number of tokens signaled by the outputs before the cut: a1, ..., an. Every output of that set receives a number of tokens as signaled by the output satoshis' value.
     * The cut output, and every other output zi, signaling zero, that is directly after the cut, receive the POC. This means that the transactions that will spend the POC will be a continuation of this issuance and a continuation of every issuance that gave the POC to the this transaction.
 - cut, b1, ..., bm with m>0 (a cut alone is a case of the fourth type)
     * This is a particular case of the first group, having n=0 and m=0. This transaction *closes the issuance forever*. Every token's chain that ends into this transaction is closed as well.
     * It generates no tokens and there are no other outputs that can continue the issuance in the future.
     * If b1 or b2 have a signal of zero and m>2, this is a token offering issuance transaction. It will be described in a following section.
 - a1, ..., an, c(POC) with n>0
     * This is an open issuance. It generates the number of tokens that are signaled in a1, ..., an. The last output c will not receive tokens.
     * The last output c will receive the POC. A following transaction that spends the output c is an issuance transaction of the same type of token.
     * The fact that c is a cut (or not) does not modify the behavior of the transactions of this group
 - c(POC) (single output, also seen as the previous one, with n=0)
     * This transaction generates no tokens at all.
     * The output c receives the POC. Thus a following transaction that spends the output c is an issuance transaction of the same type of token.

Notes on token issuances
------------------------
The number of tokens generated by an issuance transaction is always the sum of the signals of all the outputs, excluding the last one and the outputs that are listed after a cut. Thus the number of tokens sent to each output, that receives tokens, is always the number signaled by the output.

Who has the power to generate other tokens of the same issuance (POC):

 - If there is no cut, the issuance is open and the transaction that will spend the last (biggest) output can continue to generate token of the same type.
 - If there is a cut, in a position different than the first, the issuance is open. The cut output will be the input of a following transaction that issues more tokens of the same issuance chain. The following transaction can close tha chain, or can be an open issuance, thus having another output that will continue the generation chain.

In order to close forever the issuance of tokens, the transaction should have a cut as the first output and have more than one output.

Transfer of tokens
------------------
Every bitcoin transaction spends all the tokens' content of the inputs and sends them to the outputs. Some of the outputs receive the number of tokens exactly stated in the last five digits of the satoshis sent (the signal), in a way similar to an issuance transaction.

A transaction can be seen as having one of the three following shapes (ai means an output that is not a cut, bi and c are outputs that can be cut):

 - a1, ..., an, cut, b1, ..., bm, c (transactions with a cut) (n=0 is described here)
     * No output (bi) after a cut receives tokens.
     * Tokens will be assigned to outputs a1, ..., an trying to follow the signal as follows:
         - If there are enough tokens, the tokens signaled by the first output are assigned to that output.
         - If there are still remaining tokens, the tokens are sent to the following output based on the signal.
         - This continues until there is a cut or the tokens signaled by an output are more than the remaining tokens. In these cases:
             * If there is a cut, it receives all the remaining tokens.
             * If there is an output receiving more tokens than the remaining tokens (we define it a "remaining error"), the output receives no token at all. No other output will receive tokens after this and all the remaining tokens will be sent to the last output c (thus, if there is a cut in the transaction, the algorithm "jumps" the cut).
             * If there is a "remaining error" and the transaction is a special transaction as defined in the next section, and the number of tokens in input is exactly the same of the two types (big and small) that are the result of a previous split or join special transaction, the "remaining error" output gets one of the smallest tokens involved. This will be better explained in the following section about "special transactions".
     * If the first output is a cut, and the transaction is not a special one as defined below in the document, the last output (c) receives all the tokens
 - a1, ..., an, c (ai is not a cut, for every i; c can be a cut)
     * The tokens are assigned to a1, ..., an as described in the previous group.
     * The last output c receives all the remaining tokens. This behavior is not modified by the fact that the last output is a cut.
 - c (single output transaction, also seen as the previous one, with n=0)
     * The output receives all the tokens received from the inputs

Transactions receiving both the POC of an issuance and some tokens of the same issuance
---------------------------------------------------------------------------------------
The protocol is designed such that a transaction of an issuance chain never issue new tokens to an output, that receives the POC of the same type of token. But two different inputs can give to a transaction both some tokens and the POC of the same type of token. In this case, there is a double role for the transaction that is both a continuation of the issuance and a transfer transaction sending tokens of the same type.

In this case, the tokens will be allocated as defined in the following four different shapes of transaction:

 - a1, ..., an, cut, b1, ..., bm, c (transaction with a cut)
     * The generated tokens are sent to the outputs a1, ..., an as described in the definition of an issuance of tokens
     * All the tokens received in input of the same type of the issuance we are continuing will be sent to the cut output
 - a1, ..., an, c (transaction without a cut, or with c that is a cut: ai is not a cut, for every i)
     * The generated tokens are sent to the outputs a1, ..., an as described in the definition of an issuance of tokens transaction
     * All the tokens received in input, of the same type of the issuance we are continuing, will be sent to the last output c
 - cut, b1, ..., bm
     * The issuance will be closed and all the tokens will be given to the last output bm. The behavior described in the issuance transaction and in the transaction sending tokens do not influence each other, in this case.
     * If it is a special transaction, as defined below, there is no overlap between the definitions. The issuance chain is closed and the received tokens will be given as defined.
 - c only
     * The definitions of issuance transaction and transfer transaction can be used. The issuance will remain open and the address will receive all the tokens received from the inputs

Since both the first and the second group of transactions are giving the POC to the same output that receives the tokens, the output will continue to carry both the tokens received and the POC. This delegates someone to issue new tokens and allocates some tokens from a previous issuance that are still not assigned.

Split and join transactions
---------------------------
A split or join transaction is one that has one of the following formats of outputs:

 - cut, a1, ..., an, z, b1, ..., bm (z is an output signaling zero, like a cut)
 - cut, a1, ..., an, c

having the added condition that the sum of the signals of the outputs a1, ..., an is:

 - equal to the number of tokens received in input divided by 1000 (we call it a join transaction), or
 - equal to the number of tokens received in input multiplied by 1000 (we call it a split transaction)

Since the presence of these two extra conditions, the fact that a transaction is a join or split transaction, or it is not (hence it is a simple transfer transaction), depends on the number of tokens received in the input. A given transaction can be both split or join for some type of tokens, and normal for other types of tokens.

Note: this is the same format that closes an issuance chain. If the transaction receives both POC and tokens of the same type, the transaction chain will be closed and the received tokens will be sent as described here.

Note: this is also the format of a transfer transaction that assigns to the change c or bm, the token received in the input. But, if a transaction is a special one of the first two types, that behavior should not be considered and no tokens will be transferred to the change.

The split transaction generates a new type of tokens with a value that is one thousandth of the value of the type of tokens received in the input. This new type can be mixed with tokens generated by other similar split transactions, based on the same original token. Split tokens have the same value and can be joined in the future with join transactions.

The join transaction generates a new type of tokens with one thousand times the value of the type of tokens received in the input. This new type of token can coexist with tokens generated by other similar join transactions, based on the same original token. Joined tokens from the same original token, have the same value and can be split in the future with split transactions. Thus becoming again original tokens.

In a special transaction of the second group, without "the second cut" z, the change is mandatory and does not receive tokens. This means that the number of tokens sent is summed up without the last output. If the number is not correct, then it is not a split or join transaction.

Tokens split or joined are of a different type than their original source. This means that they can coexist in the same output and will never mix together. Thus a output having 3 big tokens and 456 tokens obtained by a split transaction, seems to have 3.456 tokens, but, in fact, has 3 tokens of a type and 456 tokens of another type (the second type is referred as the original type with a 0.001 unit of measure).

Note: as described below, there is a procedure of separating tokens of different types contained in the same output. This procedure will not work if the two type of tokens are present in the same output in the same number. Thus if an output contains exactly 3.003 tokens (3 big and 3 small), the tokens cannot be separated anymore. This is why we introduced, in the transfer transaction definition, the rule that assigns in this case one single token of the smallest type to the "remaining error" output.

Token offering issuance transactions
------------------------------------
A token offering issuance transaction is a transaction having one of the following formats (z is an output signaling zero, like a cut; r and s are outputs that signal a value greater than zero; the group of outputs (t1, t2, z) is optional; t1 or t2 can signal zero, but not both):

 - cut, z, r, (t1, t2, z,) a1, ..., an, c
     * price of tokens are predefined
 - cut, s, z, (t1, t2, z,) a1, ..., an, c
     * price of tokens are not predefined

The tokens will be assigned to one of the outputs of every transaction that sends bitcoin to the address of the outputs r or s, as follows:
 - if the sending transaction has only two outputs (r, c), (c, r), (s, c) or (c, s), the "other" output c receives the tokens.
 - if the sending transaction has more than two outputs, the last (biggest) output that is not the one sending bitcoins to r or s, will receive the tokens.
 - if the sending transaction has only one output, the generated tokens will be assigned to the output r or s itself. This can be considered as a donation: it generates tokens, but the tokens remain in the availability of the issuer.
 - since the number of token emitted is always an integer, the remaining satoshis are not considered in the number of tokens issued and are sent to the issuer without any token generation.

Note: this is the second place, in this document, where the bitcoin address of an output is used. The other place regards the alternate names of an issuance. Everything else in the protocol is based on outputs, not addresses.

If the group (t1, t2, z) is present, it signals how many token will be issued. The total number of tokens that will be issued is the number signaled by t1 * 10^6 + the number signaled by t2. In any block, the issuance can be closed by the transaction that spends the outputs r or s.

Timeline:
 - The offer starts in the block that contains the token offering issuance transaction. Every transaction of the starting block receives tokens, without order.
 - If there is a defined total number of tokens, the issuance will end when the total number of tokens has been reached.
     * Inside the last block, the transactions are considered in the order they are listed. So if a transaction takes the last tokens, every other transaction sending bitcoins to r or s, do not receive tokens.
 - The transaction that spends the outputs r or s ends the issuance. This transaction suspends the issuance even if a defined number of tokens was defined in the token offering issuance transaction.
     * In case of an issuance suspeded, or ended, by a transaction spending r or s, every transaction of the block containing the spending transaction will be considered valid as a receiver of tokens.
     * Thus, sending bitcoins to the address of the outputs r or s will be considered as part of the offering, only if it is included in a block between the block of the transaction that has r or s as an output (start), and the block of the transaction that spends the output r or s (end), inclusive.

A token offering issuance transaction of the first type permits to set a rate, and to issue tokens every time bitcoins are received by an address. The rate is defined by the number signaled by the output r. One token will be issued for every r satoshis received.

A token offering issuance transaction of the second type does not set a predefined rate at the start. The rate will be defined by the transaction that closes the issuance by spending the output s. The first (smallest) output of the closing transaction, or the first output after the cut (if a cut is present), will signal the rate. This type of token offering issuance, having the price defined at the end, permits to issue token based on parameters related to the issuance itself. This is the case, for example, of Dutch Auctions.

Note: A token offering issuance transaction can be seen as a transfer transaction, that sends all the tokens that receives to the output c.

Note: the type of token issued is defined by the token offering issuance transaction, seen as an issuance transaction. Since a token offering transaction is also the closure of some issuing chains, this means that the same token offer will issue different type of tokens. In fact, a different type of token will be issued for every issuance chain that ends with the same token offering issuance transaction. Thus a token type can be first issued in a controlled way (this is usually called pre-ICO) and then the rate can be stated, and the same type of token can be offered to the public (this is usually called the ICO). Since the token offering issuance transaction closes the issuance forever, there is the guarantee that no other tokens of the same type will ever be issued after the offer is closed. In order to offer tokens at different prices, multiple issuance transactions can be generated with POCs originating from the same issuance chain.

Atomic transactions between bitcoins and tokens
-----------------------------------------------
Using the cut signal and software that allows full "coin selection", it's possible to make atomic exchange transactions. The outputs before the cut will determine who will receive the tokens and the following outputs will define the rest of the transaction. Both the changes (the one of the token wallet and the one of the Bitcoin wallet), should be in the second set (after the cut). Since the cut will receive the remaining tokens, it is suggested that the cut is sent to the seller of tokens. Using this method, the remaining tokens can be sent without involving a calculation of the remaining tokens. The outputs of an atomic exchange transaction will have the following format (seller is the token seller, buyer is the token buyer).

 - a1: tokens sent from the seller to the buyer
 - a2: tokens sent from the seller to the buyer
 - cut: part of the bitcoin payment sent from buyer to seller
 - b1: part of the bitcoin payment sent from buyer to seller (or change sent from seller to buyer, if the price to be paid is less than the value of the cut)
 - b2: Bitcoin change sent to the token wallet
 - b3: Bitcoin change sent to the bitcoin wallet

It is impossible to make an atomic exchange transaction if the wallet in use does not allow coin selection.

Cross token atomic transactions
-------------------------------
Let's say that Alice wants to sell a number x of tokens of type Ta and Bob wants to pay using y tokens of type Tb. Token of type Tb are of lesser value than the tokens of type Ta, so Bob will pay more Tb tokens and Alice will pay fewer Ta tokens (x < y). Let's say that the transaction spends an output from Alice containing BTCa bitcoins and *exactly* x tokens, while Bob sends to the same transaction BTCb bitcoins and a number z of tokens of type Tb. Since z > y, Bob will receive a change c in tokens of type Tb.

Alice managed the previous transactions so that a fixed number x of tokens can be sent as the input with a number BTCa of bitcoins. Bob is not required do the same, because there is the cut that gives the remaining tokens back to Bob. In order to simplify let's say that there is another input giving the fee for the transaction and the Bitcoin assigned to each output will be calculated accordingly.

The atomic transaction can be made by signaling with the first output the number y of tokens that Bob should pay to Alice. This output will go to Alice. Since y is higher than x, all the x tokens of type Ta will go to the change (directed to Bob), while the y tokens of type Tb will go to the first output. A following cut can be used to send the change to Bob. The addresses following the cut can be used as changes of bitcoins.

The inputs of the transaction will have a content in Bitcoin and tokens as follows:

 - Alice will spend an output having BTCa bitcoins and containing *exactly* x tokens of type Ta
 - Bob will spend an output having BTCb bitcoins and containing y + c tokens of type Tb

The outputs of the transaction will have the following form:

 - Bitcoin sent: BTCa1; Signal sent: y; Directed to Alice (the output gets y tokens of type Tb, but does not get any token of type Ta, because x < y)
 - Bitcoin sent: BTCb1; Signal sent: cut; Directed to Bob (no token of type Ta given, but receives c tokens of type Tb)
 - Bitcoin sent: BTCa - BTCa1; Signal sent: not important; Directed to Alice (no token sent, but useful to send a change in Bitcoin to Alice, if needed)
 - Bitcoin sent: BTCb - BTCb1; Signal sent: not important; Directed to Bob (this output gets number x tokens of type Ta)

Cross token atomic transactions in the case of the same number of tokens to be exchanged
----------------------------------------------------------------------------------------
The atomic transactions described above do not work if the value of tokens of type Ta is equal to the value of tokens of type Tb. In this case, there is no way of doing an atomic exchange.

Let's say that we need to do a transaction between two tokens that have the same value: TetherA and TetherB. Let's say that Alice and Bob want to change 199 tokens. The atomic transaction cannot be made, but, with a small risk, two transactions can be made. The first will be an atomic transaction giving 100 tokens of type TetherA from Alice to Bob and receiving 99 of type TetherB back, and the second will be 99 to 100.

How to separate different types of token
----------------------------------------
Let's say that an output contains two different types of tokens of interest to the user. Is there a way to separate the tokens so that they can be sent to different outputs? If the tokens are exactly the same number, there is not. If the tokens are two different numbers: x tokens of type A and y tokens of type B, then the separation can be done. Since the "remaining error" of an output goes to the change, we can send the higher value of the two and have the change receive the lower. We assume that x < y.

Let's call A1 the output that will receive A and B1 the output that will receive the tokens of type B.

The transaction will be similar to the cross token atomic transaction:

 - Signal sent: x (the output gets x tokens of type B, but does not get any token of type A, because x < y)
 - Signal sent: cut (no token of type A given, but receives a change in token of type B if the previous signal was less than y)
 - Other outputs
 - Signal sent: not important (this output gets number x tokens of type A)

Guidelines
==========
There are some suggestions that, if followed by the user, permit managing tokens in a simple manner, without technical knowledge of the rest of the protocol, with plausible deniability. This can be done using any existing wallet.

The guidelines described here are based on a wallet that will be "consolidated". This means that all the outputs of the wallet are linked toghether. In some cases, this behavior diminish the level of privacy of the user. Thus, it is advised to use a number of different wallets, in order to reach the desired level of privacy.

Plausible deniability: how to use a wallet to manage tokens
-----------------------------------------------------------
Some of the protocol's operations are designed to be managed using a coin selection software, however, any wallet without coin selection can be used to generate, send, or receive tokens. The option to use any existing Bitcoin wallet is the base of the plausible deniability of the protocol. The user can send, receive and generate tokens by using any wallet in a way that seems a normal use of the Bitcoin protocol to manage bitcoins.

Thus, the guidelines in this section are based on a use of a wallet by a user without involving any "coin selection".

In order to send or generate tokens, the user needs to have, at any time, only one output in the wallet. Let's call it a "consolidated" wallet. In order to consolidate a wallet:

 - Send all the bitcoins contained in the wallet to a new address of the same wallet

If the user departs from these guidelines by mistake, he can "fix" his wallet and re-consolidate it without losing the tokens contained in the wallet. If the wallet is consolidated, it remains consolidated while tokens are generated or sent, and while bitcoins from the wallet are spent. If bitcoins or tokens are received by any address of the wallet, then the wallet needs to be consolidated again.

Issuance of tokens
------------------
In order to generate tokens:

 - Consolidate the wallet if it is not already consolidated.
 - Send a minority of the bitcoins contained in the wallet to a new address (outside of the wallet). The last five digits of the satoshis sent are the number of tokens generated.
 - From the same wallet, other tokens can be generated by sending again a number of satoshis, having the last five digits that are the number of tokens to issue to the new address.
 - The value of bitcoins sent should always be less than the bitcoin that remains in the wallet
 - If during the process of generating tokens the wallet receives bitcoins, it should be consolidated again before continuing to generate tokens.
 - The type (or name) of tokens will be the txid of the transaction. If the transaction sends bitcoin to a new, never used, address, the address can be used as the name of the tokens, as well.

In order to give the power to generate new tokens to another person:

 - Send all the Bitcoin content of the wallet to the other person, with a single transaction

In order to close an issuance:

 - To close the issuance and guarantee that no other tokens of this type will ever be generated again, send to another address of the same wallet a number of bitcoins with the last five digits of the satoshis that is zero. Be aware that this shouldn't be all the content of the wallet. If all the content of the wallet is sent to some address, the issuance will not be closed. Instead, this gives to the receiver the power to generate new tokens.

Spending bitcoins and not tokens
--------------------------------
In order to spend bitcoins from the wallet without sending any tokens, the user should spend less than half of the bitcoin value contained in the wallet, and:

 - Spend a number of satoshis where the last five digits are all zeros,
 or
 - Spend a number of satoshis where the last five digits are a number greater than the tokens that are in the wallet,

Transfer of tokens
------------------
In order to send tokens to another person:

 - Consolidate the wallet if it is not already consolidated.
 - Send a value less than half of the content of the wallet and having the number of satoshis where the last five digits are the number of tokens that need to be transferred,
 or
 - Send all the bitcoins of the wallet (even if the wallet is not consolidated).

If the user sends all the content of the wallet to a single address (no change), then he's emptying the token content from the wallet, as well. All the tokens will go to the address and nothing will remain to the user.

In order to receive tokens from other users:

 - Give to the other person a Bitcoin address of the wallet and ask to send tokens as explained above.
 - If the wallet was empty before of receiving tokens, then it is already consolidated. Instead, if the wallet already had some bitcoins, then the wallet needs to be consolidated before sending or generating tokens.

Effects of the use of these guidelines
--------------------------------------
When using the guidelines, the number of tokens sent to the recipient is always stated in the last five digits of the satoshis sent. There are three exceptions:

 - In a single output transaction, all the tokens of the wallet will be sent to the recipient.
 - In a transaction where the amount of satoshis sent ends with five zeros, no tokens are sent.
 - In a transaction sending more tokens than the number of tokens of that type held in the wallet, no tokens are sent.

Technical notes
---------------
 - Sending a number of bitcoins that is greater than half of the bitcoins contained in the wallet brings to unpredicted results.
 - Thus, if there are not enough bitcoins to continue to operate, the wallet needs to be "re-charged" by sending some bitcoins to it. By doing so, there will be more than one UTXO in the wallet. This departs from "consolidated mode" and the wallet needs to be consolidated again.
 - A consolidated wallet contains only one UTXO.
 - Every transaction made from a consolidated wallet contains only two outputs: one is the address outside of the wallet, and the other is the change.
 - Every transaction spends all the content of the wallet.

Reference Implementation
========================
A reference implementation will be included when the protocol will be reviewed and accepted by the community.

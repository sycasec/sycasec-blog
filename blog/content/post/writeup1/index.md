+++
title = 'Writeup1'
date = 2024-03-09T20:31:16+08:00
draft = false 
authors = ["Gil Brian Perez", "Alwyn Dy", "Kyle del Castillo"]
+++
---
# IND-CPA

{{< latex >}}
{{< /latex >}}

We should give a slightly more rigorous definition of confidentiality than 'attacker can't read messages' - it's vague especially for partial reveals of information. Thus, confidentiality is defined as ciphertext $C$ should give attacker no new info about message $M$.


An experiment was made to describe this, which goes along these lines:
1. An adversary, Eve, sends two messages $M_0$ and $M_1$ to Alice.
2. Alice flips a coin (in bits, 0 or 1) to choose which message to encrypt and send back to Eve.
3. Eve can ask Alice to encrypt other messages of her choosing to try and learn about which message Alice gave her.
4. Eve guesses which message was given to her encrypted by Alice.

``` python
                            IND-CPA Game : Repeat N times                      
        ┌────────────────────────────────────────────────────────────────────┐
        │           ┌──► M_0 ───┐           Alice                            │
        │           │           │    ┌──────────────────┐                    │
        │ 1.   Eve ─┤           ├──► │  Encrypt 0 or 1  │                    │
        │       ▲   │           │    └─────────┬────────┘                    │
        │       │   └──► M_1 ───┘              │                             │
        │       │                              │                             │
        │       └──────── M_ciphertext ────────┘                             │
        │                                                         Alice      │
        │                                                     ┌───────────┐  │
        │      Eve  ─────────────► Any message ─────────────► │ Encrypt M │  │
        │ 2.    ▲                                             └─────┬─────┘  │
        │       │                                                   │        │
        │       └─────────────────── ciphertext ────────────────────┘        │
        │                                                                    │
        │                                                                    │
        │              ┌─────────────────────────────────────┐               │
        │ 3.   Eve ──► │ Guess if M_ciphertext is M_0 or M_1 │               │
        │              └─────────────────────────────────────┘               │
        └────────────────────────────────────────────────────────────────────┘
                    Eve Guesses >  50% correctly: NOT IND-CPA SECURE           
                    Eve Guesses <= 50% correctly: IND-CPA SECURE               
```

This is called `ind`istinguishability under `c`hosen `p`laintext `a`ttack, or `IND-CPA`. For an encryption method to be IND-CPA secure, `Eve` (the adversary) should not be able to guess which message Alice encrypted and sent better than a coin flip ($\frac{1}{2}$ + a negligible amount, normally from brute force methods that'd take years). 

For practical application, these rules are added:
- M0 and M1 must be the same length,
- length of plaintext is leaked,
- Eve is limited to a practical number of encryption requests (normally $O(n^{k})$ time)
- Eve can ask Alice for the encrypted message again - this means any deterministic scheme is not IND-CPA secure

That last point makes the `ECB` mode of block cipher operation not `IND-CPA` secure.

## Okay, but what's an ECB? What's a block cipher? Who's Alice?
Right, uh. A block cipher takes a message and transforms it into something else of the same length. In a few more words, this message would be a fixed-length n-bits input, and uses a shared k-bit key dictating how the algorithm would scramble the input. Plaintext goes in, ciphertext goes out, and vice versa.

<!-- -> `insert image here for the visual people ` -->

$$ Encrypt(M, K) \rightarrow C$$
$$ Decrypt(C, K) \rightarrow M$$

AES, the most common of these algorithms, fixes the block size to 128 bits. If you'd want a message longer than 128 bits, well...there are different modes of operations on how to process that. ECB, or Electronic Code Book, is one of those.

ECB is considered as the simplest block cipher mode of operation - it divides a message into 128-bit blocks (under AES), each passing through the block cipher using the agreed on key to encrypt each block. These blocks would be stitched together to form the ciphertext, and the reverse is true for decryption.


{{< figure src="ecb_enc_white.png" width="700" class="centered" >}}
{{< figure src="ecb_dec_white.png" width="700" class="centered" >}}


## So let's look at how this isn't IND-CPA secure.
Let's re-trace the events taking place:
1. Eve sends $M_0$ and $M_1$ to Alice.
2. Alice sends back an encrypted message to Eve.
3. Eve asks for encrypted versions again.
4. Since this method is deterministic, the same ciphertext is received.
5. Ciphertext blocks in different messages will be the same - similar messages with small changes can be encrypted and compared*
6. Connecting this to the plaintext allows for Eve to know which message was sent 100% of the time, every time.

So how do you get a system to be IND-CPA secure? Well, you'd probably need these:
- Diffusion - changes in one part of the plaintext should affect other parts in the ciphertext
- Randomness - the ciphertext should be different each time a plaintext is encrypted
- Block Dependency - each block should be dependent on the encryption of the previous block, to make it computationally infeasible to predict the ciphertext without the key


## Alternative Modes that are IND-CPA secure

{{< figure src="blockchain.png" width="700" class="centered" >}}
> Blockchain, but not that kind of blockchain. It's going to be huge.

Since the main reason that ECB is not IND-CPA secure is it is deterministic, we just simply have to find a way to make the encryption scheme non-deterministic, right? This way, Eve can’t just simply compare the ciphertexts.

But… how can we do this? How can we incorporate diffusion, randomness, and block dependency into our scheme? 

Luckily, the smart cryptography researchers have figured it out already! Looking at the other modes of block ciphers, we can see that they included a random variable, known as an *Initial Vector* or *Nonce*, in calculating the ciphertext. Different modes use this random variable differently, but the main thing is it is now part of the process. HOWEVER, one very important thing is that this random variable must not be reused. Every time a plaintext is encrypted, a different random variable should be used. Otherwise, it will be ECB all over again. 

Another method to make the whole encryption process complicated is to chain the encryption of the blocks. In other words, use the already encrypted ciphertext block in encrypting the next blocks. This allows us to achieve both diffusion and block dependency. A single character change in the first few blocks of the plaintext will cascade down to the succeeding block, thus drastically changing the whole ciphertext. This chaining also prevents attackers from gleaning information on blocks with similar message since these would produce different ciphertexts.
The modes that use these 3 methods are: 
- Cipher Block Chaining (CBC)
- Ciphertext Feedback (CFB)
- Output Feedback (OFB) and Counter.

For this writeup, we’ll be considering CBC because it will teach us a valuable lesson later.

### Cipher Block Chaining
Cipher block chaining works by using the `Initial Vector` (`IV`) and `XOR`ing it with the first block of the plaintext then encrypting its result with the block cipher encryption under a key to produce the first ciphertext block. This first block is then used to encrypt the second plaintext block which will then be used to encrypt the third block and so on. Although the IV is only used once, the resulting ciphertext changes every time similar plaintext message is encrypted. Moreover, since the previous ciphertext block is used to encrypt the next block, plaintext blocks with similar message will produce different ciphertexts, thus preventing attackers from just comparing blocks to glean information on the original plaintext message. 
#### Let’s play a game
The IND-CPA security of CBC is already [mathematically proven](https://www.cs.ucdavis.edu/~rogaway/papers/sym-enc.pdf). But comprehending it requires a PhD so we’ll have to use the IND-CPA game instead for us, undergraduates, to understand.
1. Eve sends two message to Alice, say `cat` and `dog`
2. Alice chooses `cat` (because they’re cute) and encrypts it, producing the ciphertext `qwe`. Alice then sends `qwe` to Eve
3. Eve asks Alice for an encryption of `cat`, `dog`, and `fat` and gets back `asd`, `zxc`, and `rty`, respectively
4. Since Eve can’t just simply compare the ciphertexts, she has no way of knowing which plaintext corresponds to the ciphertext she received at (2). Thus, Eve is defeated
#### Is CBC fully secure?
{{< figure src="hackerman.png" width="700" class="centered" >}}
> Oh yeah I'm about to hack into the mainframe

We have already shown that CBC is IND-CPA secure. But does that mean that it is totally secure? Is it already resistant to other forms of attack? Not quite. 

Currently, CBC is known to be [vulnerable to a padding oracle attack](https://learn.microsoft.com/en-us/dotnet/standard/security/vulnerabilities-cbc-mode) that could potentially reveal the plaintext to the attacker. *Another attack? Not again?*  Well, the world of cryptography is full of attacks, so get used to it.

Padding oracle attack is not as simple as our IND-CPA game so we’ll need to simplify it for our 2 remaining braincells to comprehend.

#### What is a padding oracle?
It’s simply a part of the crypto system that checks the padding of the decrypted plaintext. We have already learned that padding is added to plaintexts to account for the differences in its length, so this concept is not new to us.

This attack works by exploiting the fact that a server will either return an error or will quickly return a message when the decrypted plaintext has incorrect padding. In contrast, when the server does decrypt a plaintext with a valid padding, it will take some time to send it since the plaintext will be processed by the server.

*So what? The attacker still doesn’t have the key so the secret remains to be secret, right?*  Well… the attacker still knows the `IV` and has access to it since it is attached to the beginning of the ciphertext.

The attacker can modify the IV, passes it to the server where the ciphertext is decrypted and, with some Maths magic, eventually reveal the original plaintext. This is just a hand-wavey explanation since it is beyond the scope of this writeup, but a very important lesson we can learn from this is that any, and just ANY, information that an attacker can modify or glean information about the plaintext or the decryption pipeline can be used to subvert the system. Attackers are clever ~~turds~~ people that can take advantage of a system. 
## Summary
In summary, we got a more detailed definition for confidentiality, specifically IND-CPA. A block cipher changes a message into an encrypted message; a simple implementation like ECB doesn't meet the requirements to be IND-CPA secure like diffusion and randomness. 

The known vulnerabilities of CBC speaks of the core of cybersecurity: no functioning crypto system is 100% secure. Even though a system may be proven secure on a particular exploit, it doesn’t automatically mean that it will remain secure against other exploits, especially that efforts to subvert crypto systems are always present. Despite the never-ending cat-and-mouse saga between crypto systems and attackers, this competition will, in the end, benefit us all. After all, competition incites innovation.

---

#### References
- Bellare, M., Desai, A., Jokipii, E., & Rogaway, P. (1997, October). A concrete security treatment of symmetric encryption. In _Proceedings 38th Annual Symposium on Foundations of Computer Science_ (pp. 394-403). IEEE
- blowdart. (2022, September 8). _CBC decryption vulnerability - .NET_. Microsoft.com. https://learn.microsoft.com/en-us/dotnet/standard/security/vulnerabilities-cbc-mode
- Sohl, E. (2021, February 17). _Cryptopals: Exploiting CBC Padding Oracles_. NCC Group Research Blog. https://research.nccgroup.com/2021/02/17/cryptopals-exploiting-cbc-padding-oracles/
- Symmetric-key cryptography. Computer Security. (n.d.). https://textbook.cs161.org/crypto/symmetric.html 

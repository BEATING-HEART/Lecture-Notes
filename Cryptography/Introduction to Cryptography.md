# Cryptography Introduction



### Definition

Cryptography is the art and science of keeping messages secure.

Cryptography is the science of using mathematics to encrypt and decrypt data.



### Terminologies

A message is **plaintext** (sometimes called **cleartext**)

The process of disguising a message in such a way as to hide its substance is **encryption**. 

An encrypted message is **ciphertext**.

The process of turning ciphertext back into plaintext is **decryption**.
A **cipher** (or **cypher**) is an  algorithm for performing  encryption or decryption — a  series of well-defined steps  that can be followed as a  procedure

A **cryptosystem** (**cipher system**) is an implementation of cryptographic techniques and their accompanying infrastructure to  provide information security services.  The various  components of a basic cryptosystem are as follows −

* Plaintext
* Encryption Algorithm
* Ciphertext
* Decryption Algorithm
* Encryption Key
* Decryption Key

While cryptography is the science of securing data, **cryptanalysis** is the science of analyzing and  breaking secure communication.

**Cryptanalysts** are also called attackers.

**Cryptology** embraces both cryptography and cryptanalysis



### History of Cryptography

These ideas further fueled the natural need of people to communicate secretly with selective recipient which  in turn ensured the continuous evolution of cryptography as well.

* Hieroglyph - Some 4000 years ago, the  Egyptians used to communicate by messages written in hieroglyph.
* Caesar Shift Cipher - Caesar Shift Cipher, relies on shifting the letters of a message by  an agreed number (three was a common choice), the recipient of  this message would then shift the letters back by the same  number and obtain the original message.
* Kamasutra Cipher - The techniques involves randomly pairing letters of the alphabet, and then substituting each  letter in the original message with its partner



### Goal and Services

**Goal**: The primary goal of cryptography is to secure important data on the hard disk or  as it passes through a medium that may not be secure itself. Usually, that medium is a  computer network.

**Services**: Cryptography can provide the following services: 

* Confidentiality (secrecy)  
* Integrity (anti-tampering) 
* Authentication 
* Non-repudiation.

> **Confidentiality (secrecy)**:
>
> * Ensuring that no one can read the message except the intended receiver 
> * Data is kept secret from those without the proper credentials, even if  that data travels through an insecure medium
>
> **Integrity (anti-tampering)**:
>
> * Assuring the receiver that the received message has not been altered in  any way from the original.
>
> **Authentication**:
>
> * Cryptography can help establish identity for authentication purposes The process of proving one's identity. (The primary forms of host-to-host  authentication on the Internet today are name-based or address-based,  both of which are notoriously weak.)
>
> **Non-repudiation**:
>
> * A mechanism to prove that the sender really sent this message



### Types of Cryptography

---------------

##### Symmetric Key Cryptography:

Also known as Secret Key Cryptography or Conventional Cryptography, Symmetric Key  Cryptography is an encryption system in which **the sender and receiver of a message share a  single, common key** that is used **to encrypt and decrypt the message**.

The Algorithm use is also known as a secret key algorithm or sometimes called a symmetric algorithm.

A key is a piece of information (a parameter) that determines the functional output of a  cryptographic algorithm or cipher. **The key for encrypting and decrypting the file had to be known to all the recipients.** Else, the message could not be decrypted by conventional means.

**Examples:**

* **Data Encryption Standard (DES)** - DES uses a 56 bit key and maps a 64 bit input block of plaintext onto a 64 bit output block of ciphertext. 56  bits is a rather small key for today's computing power.
* **Triple DES** - Triple DES was the answer to many of the shortcomings of DES. Since it is based on the DES algorithm, it is  very easy to modify existing software to use Triple DES. It also has the advantage of proven reliability and a  longer key length that eliminates many of the shortcut attacks that can be used to reduce the amount of  time it takes to break DES.
* **Advanced Encryption Standard (AES)** - The  standard comprises three block ciphers, AES-128, AES-192 and AES-256, adopted from a larger collection originally published as Rijndael. Each AES cipher has a 128-bit block size, with key sizes of 128, 192 and 256 bits, respectively. The AES ciphers have been analyzed extensively and are now used worldwide, as was the case with its  predecessor, the Data Encryption Standard (DES).
* **The International Data Encryption Algorithm (IDEA)** - It uses a 128 bit key to encrypt a 64 bit block of plaintext into a 64 bit block of ciphertext.  IDEA's general structure is very similar to DES, it performs 17 rounds, each round taking 64 bits of  input to produce a 64 bit output, using per-round keys generated from the 128 bit key.
* **others** - Lucifer . Madryga . FEAL . REDOC . LOKI . GOST . CAST . Blowfish . Safer . Crab . RC5 

**Problems:**

* Symmetric-key systems are simpler and faster; **their main drawback is that the two parties must  somehow exchange the key in a secure way and keep it secure after that.**

* **Key Management caused nightmare for the parties using the symmetric key cryptography.** They were  worried about how to get the keys safely and securely across to all users so that the decryption of the  message would be possible. This gave the chance for third parties to intercept the keys in transit to  decode the top-secret messages. Thus, if the key was compromised, the entire coding system was  compromised and a “Secret” would no longer remain a “Secret”.

* **This is why the “Public Key Cryptography” came into existence.**

-----------------

##### Asymmetric Key Cryptography

Asymmetric cryptography , also known as Public-key cryptography, refers to a cryptographic algorithm which **requires two separate keys, one of which is private and one of which is public. The public key is used to  encrypt the message and the private one is used to decrypt the message.**

Public Key Cryptography is a very advanced form of cryptography.

**Examples:**

* **Digital Signature Standard (DSS)** - Digital Signature Standard (DSS) is the digital signature algorithm (DSA) developed by the U.S. National Security  Agency (NSA) to generate a digital signature for the authentication of electronic documents.
* **Algorithm - RSA** - RSA (Rivest, Shamir and Adleman who first publicly described it in 1977) is an algorithm for public-key  cryptography. **It is the first algorithm known to be suitable for signing as well as encryption, and one of  the first great advances in public key cryptography.** RSA is widely used in electronic commerce protocols, and is believed to be secure given sufficiently long  keys and the use of up-to-date implementations.
* **ElGamal** - ElGamal is a public key method that is **used in both encryption and digital signing.**  The encryption algorithm is similar in nature to the Diffie-Hellman key agreement protocol. It is used in many applications and **uses discrete logarithms**. ElGamal encryption is used in the free GNU Privacy Guard software

### Hash Functions

A cryptographic hash function is a hash function that  **takes an arbitrary block of data and returns a fixed-size bit string**, the cryptographic hash value, such that **any  (accidental or intentional) change to the data will (with  very high probability) change the hash value.**

The data  to be encoded are often called the message, and the  hash value is sometimes called the **message digest** or  **simply digest**.

**The ideal cryptographic hash function has four main properties:** 

* it is easy to compute the hash value for any given message
* it is infeasible to generate a message that has a given hash
* it is infeasible to modify a message without changing the hash
* it is infeasible to find two different messages with the same hash.

**Examples:**

* Snefru
* N-Hash
* MD2, MD4, MD5, MD6
* SHA1, SHA2

**Hash Collision:**

In March 2005 Xiaoyun Wang and Hongbo Yu of  Shandong University in China created a pair of files that share the same MD5 checksum hence prove that there is a collusion when using MD5

**SHA** - The Secure Hash Algorithm (SHA) hash functions are a set of cryptographic hash functions. Because of the successful attacks on MD5, SHA-0 and theoretical attacks on SHA-1, NIST  perceived a need for an alternative, dissimilar cryptographic hash, which became SHA-3. In October 2012, the National Institute of Standards and Technology (NIST) chose the Keccak algorithm as the new SHA-3 standard.
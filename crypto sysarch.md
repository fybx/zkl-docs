# zk-Lokomotive Cryptographic System Architecture

Yigid BALABAN, <fyb@fybx.dev>

Revision 1
21/08/2024

## System Elements

The system elements are/will be described and discussed in the ZKL System Architecture document. This section is to provide a reminder/reference.

### 1. Key Derivation Service

The Key Derivation Service (or KDS for short) provides 
1. a deterministic Curve25519 keypair generator from BIP-39 mnemonics,
2. a pseudo-random BIP-39 mnemonic generator through web-bip-39 package.

### 2. Cross-chain Identity Registry

The Cross-chain Identity Registry (or CCIR for short) provides a method to look up identities and their corresponding public keys.

### 3. Encrypted File Storage

The Encrypted File Storage is a distributed storage solution that allows the recipient to retrieve payloads that were uploaded for them.

### 4. Client

The client (the sender) generates the encrypted payload to be sent to the reciever, whose public key is retrieved via the CCIR. The workflow of sending an encrypted file for a recipient is described in the next section.

## Workflows

### Sending a file

#### Definitions
$K_{r}:\text{Recipient's public key on Curve25519}$
$K_{e}:\text{A symmetric key derived for the file to be sent, 256 bits}$
$E(K_{e}):\text{The symmetric key, encrypted using ECIES}$
$F:\text{The file contents, in plaintext}$
$F_{c}:\text{The file contents, in ciphertext}$
$IV:\text{The initialization vector required for AES-GCM-256}$
$P:\text{The payload, what is sent to the recipient}$

#### Workflow

1. The $K_{r}$ is retrieved from the CCIR.
2. The $K_{e}$ is generated randomly for the file, $F$.
3. File $F$ is encrypted using AES-GCM-256 with encryption key $K_{e}$, and a randomly generated initialization vector, $IV$.
4. The $K_{e}$ is encrypted using ECIES, which is discussed in detail, in section “Key Encryption through ECIES”. This results in a new encrypted data, $E(K_{e})$.
5. The payload $P$ is created by concatenating $F_{e},\;IV\;E(K_{e})$
6. The payload is uploaded to the EFS.
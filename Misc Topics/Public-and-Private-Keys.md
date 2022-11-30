# Public and Private keys
- For a *private key* system, The sender and receipient share a private key (usually a long stream of random bits)
    - The message is encrypted with the shared key (i.e. by XORing the message and then reversing it when unencrypting), and this encrypted message is public (but since it is encrypted it cannot be easily be broken into)
- For a *public key* system, there exists a public key (U) and private key (K) pair 
    - The recipient publishes the public key, U, and the sender publishes its message encrypted with the public key
        - The recipient then unencrypts the message with the private key
    - Effectively, encrypting is done with the public key and unencrypting is done with the private key or vice-versa
        - Thus, the private key can be used to sign and encrypt a message, which can then be verified by others who have the public key to unencrypt it
            - This prevents other parties from claiming to be the private key holder  
# Encryption

## Encryption in flight (SSL)

- End-to-end encryption: data is encrypted before being sent and decrypted after is received
- Only the sender and the receiver should be able to decrypt/encrypt the messages
- SSL certificates help with encryption (HTTPS)
- Encryption in flight ensures no MITM (man in the middle attack) can happen

## Server side encryption at rest

- Data is encrypted after being received by the server
- We should know that the server stores encrypted data. If the server is hijacked, the data can not be retrieved in a readable way
- Data is decrypted before being sent back to the client
- The encryption/decryption key must be managed somewhere and server must have access to it

## Client side encryption

- Data is encrypted by the client, the server should never be able to decrypt it
- Data will be decrypted by a receiving client
- We can use Envelope Encryption to achieve client side encryption
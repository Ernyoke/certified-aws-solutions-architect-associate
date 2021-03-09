# CloudHSM

- KMS => AWS manages the software encryption
- CloudHSM => AWS provisions encryption hardware, we have to use our own client to do the encryption
- HSM device is tamper resistant, FIPS 140-2 Level 3 compliant
- HSM cluster can be set across multiple AZ (HA)
- Supports both symmetric and asymmetric encryption (SSL/TLS keys)
- No free tier available for CloudHSM
- Redshift supports CloudHSM for database encryption and key management
- CloudHSM is also a good idea for SSE-C encryption for S3
- **AWS can not recover credentials keys in case they are lost!**
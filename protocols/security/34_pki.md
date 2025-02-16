# PKI (Public Key Infrastructure)

## Overview
PKI is a framework for managing digital certificates and public-key encryption. It provides the foundation for secure communication and authentication in various protocols like SSL/TLS, S/MIME, and digital signatures. PKI ensures confidentiality, integrity, and non-repudiation through a trusted hierarchy of certificates.

## Technical Details

### Components

1. **Certificate Authority (CA)**
   - Root CA
   - Intermediate CA
   - Policy CA
   - Issuing CA

2. **Registration Authority (RA)**
   - Identity verification
   - Certificate requests
   - Key generation
   - Certificate distribution

3. **Digital Certificates**
   - X.509 standard
   - Certificate fields
   - Extensions
   - Validity periods

4. **Certificate Revocation**
   - CRL (Certificate Revocation List)
   - OCSP (Online Certificate Status Protocol)
   - Delta CRLs
   - OCSP stapling

### Certificate Lifecycle

1. **Certificate Request**
   - CSR generation
   - Key pair creation
   - Subject information
   - Extensions

2. **Certificate Issuance**
   - Validation
   - Signing
   - Distribution
   - Installation

3. **Certificate Management**
   - Renewal
   - Revocation
   - Recovery
   - Archival

## Implementation

### Basic Configuration

1. **OpenSSL CA Setup**
   ```bash
   # Generate root CA
   openssl genrsa -aes256 -out rootCA.key 4096
   openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 3650 -out rootCA.crt
   
   # Generate intermediate CA
   openssl genrsa -aes256 -out intermediateCA.key 4096
   openssl req -new -key intermediateCA.key -out intermediateCA.csr
   openssl x509 -req -in intermediateCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out intermediateCA.crt -days 1825 -sha256
   ```

2. **Certificate Generation**
   ```bash
   # Generate server key and CSR
   openssl genrsa -out server.key 2048
   openssl req -new -key server.key -out server.csr
   
   # Sign certificate
   openssl x509 -req -in server.csr -CA intermediateCA.crt -CAkey intermediateCA.key -CAcreateserial -out server.crt -days 365 -sha256
   ```

### Advanced Configuration

1. **Certificate Templates**
   ```bash
   # OpenSSL configuration
   [ req ]
   default_bits = 2048
   prompt = no
   default_md = sha256
   distinguished_name = dn
   
   [ dn ]
   C = US
   ST = State
   L = City
   O = Organization
   OU = Department
   CN = common.name.com
   ```

2. **CRL Generation**
   ```bash
   # Generate CRL
   openssl ca -config openssl.cnf -gencrl -out crl.pem
   
   # Revoke certificate
   openssl ca -config openssl.cnf -revoke cert.pem -crl_reason keyCompromise
   ```

## Design Considerations

### Architecture Planning
1. **Hierarchy Design**
   - Single-tier vs multi-tier
   - Trust relationships
   - Cross-certification
   - Bridge CA

2. **Scalability**
   - Certificate volume
   - CRL distribution
   - OCSP capacity
   - Backup systems

3. **High Availability**
   - CA redundancy
   - Load balancing
   - Geographic distribution
   - Disaster recovery

### Security Planning
1. **Key Protection**
   - HSM integration
   - Key ceremonies
   - Access controls
   - Backup procedures

2. **Policy Management**
   - Certificate policies
   - CPS development
   - Audit procedures
   - Compliance requirements

## Troubleshooting

### Common Issues
1. **Certificate Problems**
   - Expired certificates
   - Invalid signatures
   - Chain issues
   - Name mismatches

2. **CRL/OCSP Issues**
   - Distribution delays
   - Availability problems
   - Size limitations
   - Performance issues

3. **Key Management**
   - Lost keys
   - Compromised keys
   - Recovery procedures
   - Backup failures

### Verification Commands
```bash
# Verify certificate
openssl verify -CAfile rootCA.crt -untrusted intermediateCA.crt server.crt

# Check certificate details
openssl x509 -in server.crt -text -noout

# Test OCSP responder
openssl ocsp -issuer issuer.crt -cert cert.crt -url http://ocsp.example.com

# Verify CRL
openssl crl -in crl.pem -text -noout
```

## Best Practices

### Security Guidelines
1. **CA Operations**
   - Secure facilities
   - Dual control
   - Audit logging
   - Key ceremonies

2. **Certificate Management**
   - Strong keys
   - Regular rotation
   - Automated renewal
   - Revocation procedures

3. **Policy Implementation**
   - Clear guidelines
   - Regular reviews
   - Compliance checks
   - Documentation

### Operational Procedures
1. **Maintenance**
   - System updates
   - Key rotation
   - CRL management
   - Backup verification

2. **Documentation**
   - Procedures
   - Policies
   - Audit trails
   - Recovery plans

## Security Considerations

### PKI Security
1. **CA Protection**
   - Physical security
   - Network isolation
   - Access controls
   - Monitoring systems

2. **Key Protection**
   - HSM usage
   - Backup security
   - Transport security
   - Recovery procedures

3. **Certificate Security**
   ```bash
   # Generate CSR with strong key
   openssl req -new -newkey rsa:4096 -nodes -sha512 -keyout private.key -out server.csr
   
   # Set secure permissions
   chmod 600 private.key
   chmod 644 server.crt
   ```

## Interview Tips
- Understand PKI components
- Know certificate lifecycle
- Explain trust models
- Understand security features
- Be familiar with:
  - X.509 certificates
  - Key management
  - CRL/OCSP
  - HSM concepts
- Real-world scenarios:
  - Enterprise PKI
  - Web PKI
  - Code signing
  - Email security
- Best practices:
  - CA operations
  - Key protection
  - Certificate management
  - Policy implementation
- Advanced concepts:
  - Certificate transparency
  - CAA records
  - ACME protocol
  - Time-stamping 
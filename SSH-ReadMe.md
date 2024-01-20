# HashiCorp Vault SSH Client Signer

## Overview

This repository provides instructions on setting up an SSH Client Signer using HashiCorp Vault. This allows for centralized management and dynamic generation of SSH certificates for authentication.

## Getting Started

Follow the steps below to set up the SSH Client Signer using Vault.

### 1. Enable SSH Client Signer

```bash
vault secrets enable -path=ssh-client-signer ssh
```

### 2. Generate Signing Key

```bash
vault write ssh-client-signer/config/ca generate_signing_key=true
```

### 3. Create SSH Role

```bash
vault write ssh-client-signer/roles/administrator-role -<<"EOH"
{
  "algorithm_signer": "rsa-sha2-512",
  "allow_user_certificates": true,
  "allowed_users": "ubuntu",
  "allowed_extensions": "permit-pty,permit-port-forwarding",
  "default_extensions": [
    {
      "permit-pty": ""
    }
  ],
  "key_type": "ca",
  "default_user": "ubuntu",
  "ttl": "30m0s"
}
EOH
```

### 4. Configure SSH Server

```bash
sudo curl https://<IP>:8200/v1/ssh-client-signer/public_key -o /etc/ssh/trusted-CA.pem
```
```bash
sudo nano /etc/ssh/sshd_config
```

Add the following line to `/etc/ssh/sshd_config`:

```plaintext
TrustedUserCAKeys /etc/ssh/trusted-CA.pem
```

Restart the SSH service:

```bash
sudo systemctl restart sshd
```

### 5. Authenticate and Generate SSH Certificate

```bash
vault login -method=userpass username=alice password=passw0rd
```
```bash
vault write -field=signed_key ssh-client-signer/sign/administrator-role public_key=@$HOME/.ssh/id_rsa.pub > $HOME/.ssh/id_rsa-cert.pub
```
Optionally, you can set a custom TTL for the certificate:

```bash
vault write -field=signed_key ssh-client-signer/sign/administrator-role public_key=@$HOME/.ssh/id_rsa.pub ttl="30s" > $HOME/.ssh/id_rsa-cert.pub
```
### Allow admin ssh role
```bash
path "ssh-client-signer/sign/administrator-role" {
  capabilities = ["read", "create", "update", "list", "delete"]
}
```
For more details, refer to the [HashiCorp blog post](https://www.hashicorp.com/blog/managing-ssh-access-at-scale-with-hashicorp-vault).

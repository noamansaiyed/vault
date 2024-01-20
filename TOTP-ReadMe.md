# Vault TOTP (Time-Based One-Time Password) Secret Engine

## Overview

This repository provides instructions on setting up the TOTP (Time-Based One-Time Password) Secret Engine using HashiCorp Vault. TOTP is commonly used for two-factor authentication (2FA) and provides a time-sensitive, one-time use code.

## Getting Started

Follow the steps below to set up and utilize the Vault TOTP Secret Engine.

### 1. Enable TOTP Secret Engine

```bash
vault secrets enable totp
```

### 2. Generate TOTP Key

```bash
vault write --field=barcode totp/keys/demo generate=true issuer=vault account_name=demo@internal
```

Save the output of the above command in a file named `decode`.

### 3. Decode and Save Barcode

Use the following command to decode the TOTP barcode and save it as an image file (`totp.jpg` in this example).

```bash
cat decode | base64 -d > totp.jpg
```

### 4. Retrieve TOTP Code

To retrieve the TOTP code for the specified account (`demo` in this example), use the following command:

```bash
vault read totp/code/demo
```

## Use Case

- **Enhanced Authentication Security:** Implement TOTP-based two-factor authentication for an additional layer of security.



##  STX-ProofLoom — KYCrypt: Decentralized KYC Verification Protocol

---

### 🧾 Overview

**STX-ProofLoom** is a decentralized Know Your Customer (KYC) verification smart contract built on Clarity. It provides a secure, permissioned protocol to manage and track the verification status of blockchain addresses in a trust-minimized and auditable manner.

Key features:

* Allows users to request verification by submitting their KYC data.
* Enables contract owner to approve, reject, or revoke verification statuses.
* Tracks verifier, status, timestamp, and KYC data per address.
* Supports secure ownership transfer of the contract.

---

### 📦 Features

* 🛂 **KYC Request Handling**
  Users can submit KYC data for verification.

* ✅ **Approval & Rejection**
  Contract owner can approve or reject KYC requests.

* 🔄 **Revocation Support**
  Verified addresses can be revoked (set back to unverified) by the contract owner.

* 🔐 **Ownership Transfer**
  Ownership of the contract can be securely transferred to another principal.

* 🧾 **Detailed Metadata**
  Each address stores:

  * Status: `0` (unverified), `1` (pending), `2` (verified), `3` (rejected)
  * Timestamp of status change
  * Submitted KYC data (UTF-8 string)
  * Verifier principal

---

### 🛠️ Smart Contract Structure

#### Constants

| Name                    | Code   | Description                         |
| ----------------------- | ------ | ----------------------------------- |
| `ERR_UNAUTHORIZED`      | `u100` | Unauthorized access                 |
| `ERR_ALREADY_VERIFIED`  | `u101` | Address already submitted for KYC   |
| `ERR_NOT_VERIFIED`      | `u102` | Address not verified                |
| `ERR_INVALID_STATUS`    | `u103` | Invalid status change               |
| `ERR_INVALID_INPUT`     | `u104` | Bad input data                      |
| `ERR_INVALID_NEW_OWNER` | `u105` | Attempt to assign invalid new owner |

---

### ⛓️ Data Structures

#### Map: `verified-addresses`

```clojure
principal => {
  status: uint,          ;; 0 - unverified, 1 - pending, 2 - verified, 3 - rejected
  timestamp: uint,       ;; block-height when status was set
  kyc-data: (string-utf8 500), ;; Optional KYC metadata
  verifier: principal    ;; Who verified the address
}
```

#### Variable: `contract-owner`

Holds the principal of the current contract owner.

---

### 🔍 Read-Only Functions

* `get-verification-status(address)` → returns the full status object of an address.
* `is-contract-owner(address)` → checks if a given principal is the current contract owner.

---

### 📤 Public Functions

| Function                         | Access | Description                     |
| -------------------------------- | ------ | ------------------------------- |
| `request-verification(kyc-data)` | User   | Submit a KYC request            |
| `verify-address(address)`        | Owner  | Approve a pending request       |
| `reject-verification(address)`   | Owner  | Reject a pending request        |
| `revoke-verification(address)`   | Owner  | Revoke an existing verification |
| `transfer-ownership(new-owner)`  | Owner  | Transfer contract ownership     |

---

### 🔒 Access Control

All sensitive operations like verifying/rejecting addresses or transferring ownership require the `tx-sender` to be the current `contract-owner`.

---

### ✅ Status Flow

```text
[Unverified (0)] ──> [Pending (1)] ──> [Verified (2)]
                            │
                            └────> [Rejected (3)]

[Verified (2)] ──> [Unverified (0)]   (via revoke)
[Pending (1)] ──> [Unverified (0)]    (via revoke)
```

---

### 🧪 Error Codes

| Error Code | Description                      |
| ---------- | -------------------------------- |
| `u100`     | Caller is not authorized         |
| `u101`     | Already verified/requested       |
| `u102`     | Not verified                     |
| `u103`     | Invalid status for the operation |
| `u104`     | Input failed validation          |
| `u105`     | Invalid new owner provided       |

---

### 🧪 Deployment Notes

* On contract deployment, the deploying address becomes the initial contract owner and is initialized in the `verified-addresses` map.

---

### 📌 Example Usage

```clojure
;; User submits KYC request
(request-verification u"My KYC data")

;; Owner approves the request
(verify-address 'SP123...')

;; Owner rejects a pending request
(reject-verification 'SP123...')

;; Owner revokes a previously verified address
(revoke-verification 'SP123...')

;; Transfer ownership
(transfer-ownership 'SP456...')
```

---

### 📜 License

MIT License – free to use, modify, and distribute.

---

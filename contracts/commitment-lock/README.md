# commitment-lock

This is a simple commitment lock script for ckb fiber network.

The lock script args is the hash result of blake160(local_delay_epoch || local_delay_pubkey_hash || revocation_pubkey_hash || N * pending_htlc), to unlock this lock, the transaction must provide following fields in the witness:

- `empty_witness_args`: 16 bytes, fixed to 0x10000000100000001000000010000000, for compatibility with the xudt
- `local_delay_epoch`: 8 bytes, u64 in little endian, must be a relative EpochNumberWithFraction
- `local_delay_pubkey_hash`: 20 bytes, hash result of blake160(local_delay_pubkey)
- `revocation_pubkey_hash`: 20 bytes, hash result of blake160(revocation_pubkey)
- `pending_htlc`: A group of pending HTLCS, each HTLC is 85 bytes, contains:
    - `htlc_type`: 1 byte, high 7 bits for payment hash type (0000000 for blake2b, 0000001 for sha256), low 1 bit for offered or received  type (0 for offered HTLC, 1 for received HTLC)
    - `payment_amount`: 16 bytes, u128 in little endian
    - `payment_hash`: 20 bytes
    - `remote_htlc_pubkey_hash`: 20 bytes, hash result of blake160(remote_htlc_pubkey)
    - `local_htlc_pubkey_hash`: 20 bytes, hash result of blake160(local_htlc_pubkey)
    - `htlc_expiry`: 8 bytes, u64 in little endian, must be an absolute timestamp
- `unlock_type`: 1 byte, 0x00 ~ 0xFE for pending HTLC unlock, 0xFF for non-pending HTLC unlock
- `signature`: 65 bytes, the signature of the xxx_pubkey
- `preimage`: 32 bytes, an optional field to provide the preimage of the payment_hash

To know more about the transaction building process, please refer to the `test_commitment_lock_no_pending_htlcs` and `test_commitment_lock_with_two_pending_htlcs` unit test.

*This contract was bootstrapped with [ckb-script-templates].*

[ckb-script-templates]: https://github.com/cryptape/ckb-script-templates

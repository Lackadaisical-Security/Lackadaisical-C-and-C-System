# Memory Attestation Module Documentation

## Overview

The Memory Attestation Module provides functionality to monitor and verify the integrity of critical memory regions. It helps detect unauthorized modifications to security-sensitive data structures in memory, providing an additional layer of protection against memory tampering attacks.

## Features

- **Memory Region Registration**: Register specific memory regions for monitoring
- **Integrity Verification**: Check if monitored memory regions have been modified
- **Continuous Monitoring**: Optional automatic periodic verification
- **Critical Region Protection**: Special handling for security-critical memory regions
- **Statistics Collection**: Track memory protection metrics

## API Reference

### Initialization and Cleanup

```c
bool memory_attestation_init(bool continuous, uint32_t check_interval_ms);
void memory_attestation_cleanup(void);
```

Initialize and clean up the memory attestation module. Parameters:
- `continuous`: Set to true to enable continuous background monitoring
- `check_interval_ms`: Interval between automatic checks in milliseconds (when continuous monitoring is enabled)

### Memory Region Management

```c
int memory_attestation_register_region(void* address, size_t size, bool is_critical);
bool memory_attestation_unregister_region(int region_id);
```

Register and unregister memory regions for attestation monitoring. Parameters:
- `address`: Start address of the memory region
- `size`: Size of the memory region in bytes
- `is_critical`: Flag indicating if this is a security-critical region
- `region_id`: ID of the region to unregister (returned by register function)

Returns:
- `register`: Returns a region ID (non-negative) on success, or -1 on failure
- `unregister`: Returns true on success, false on failure

### Memory Verification

```c
bool memory_attestation_verify_region(int region_id);
int memory_attestation_verify_all(void);
bool memory_attestation_update_baseline(int region_id);
```

Functions to verify memory regions and update verification baselines:
- `verify_region`: Check if a specific region has been modified
- `verify_all`: Check all registered regions, returns count of failed verifications
- `update_baseline`: Update the baseline hash for a region

### Statistics

```c
bool memory_attestation_get_stats(MemoryAttestationStats* stats);
```

Get statistics about memory attestation. The `MemoryAttestationStats` structure includes:
- `region_count`: Number of regions being monitored
- `critical_regions`: Number of critical security regions
- `last_full_check`: Timestamp of the last full verification
- `total_bytes_protected`: Total amount of memory being monitored

## Usage Examples

### Basic Usage

```c
// Initialize attestation
memory_attestation_init(false, 1000);

// Register a critical memory structure
SecurityCredentials creds = {0};
int region_id = memory_attestation_register_region(&creds, sizeof(creds), true);

// Use the memory structure
initialize_credentials(&creds);

// Later, verify it hasn't been tampered with
if (!memory_attestation_verify_region(region_id)) {
    handle_security_violation();
}

// Clean up when done
memory_attestation_unregister_region(region_id);
memory_attestation_cleanup();
```

### Continuous Monitoring

```c
// Initialize with continuous monitoring
memory_attestation_init(true, 5000);  // Check every 5 seconds

// Register memory regions
register_critical_regions();

// Your application runs normally while monitoring happens in background

// Clean up
memory_attestation_cleanup();
```

### Handling Detected Modifications

```c
// Register a region
int region_id = memory_attestation_register_region(secure_data, data_size, true);

// If a modification is detected
if (!memory_attestation_verify_region(region_id)) {
    // Log the security event
    log_security_event("Memory tampering detected");
    
    // Restore from a secure backup
    restore_secure_data();
    
    // Update the baseline after restoration
    memory_attestation_update_baseline(region_id);
}
```

## Security Considerations

- The module doesn't prevent memory modifications; it only detects them
- For high-security applications, combine with the Memory Manipulation module for active protection
- The hash algorithm used is critical for security - ensure it's cryptographically strong
- Be careful when updating baselines, as this could accept tampered memory as the new normal
- Consider where hash values are stored - they should be protected against modification

## Performance Considerations

- Hashing large memory regions is CPU-intensive
- Balance security needs with performance requirements when setting check intervals
- Critical regions should be kept as small as possible
- Use continuous monitoring carefully in performance-sensitive applications

## Integration with Other Security Measures

The Memory Attestation module works best when combined with other security features:

1. Use with Memory Manipulation module for comprehensive protection
2. Integrate with logging system for security audit trails
3. Connect to alert system for immediate notification of tampering
4. Combine with secure boot and process isolation for defense in depth

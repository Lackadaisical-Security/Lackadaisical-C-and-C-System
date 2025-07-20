# Memory Manipulation Module Documentation

## Overview

The Memory Manipulation Module provides advanced memory management capabilities for handling sensitive data securely in memory. It offers protection mechanisms against memory-based attacks, anti-forensics capabilities, and tools for detecting sensitive information in memory.

## Features

- **Secure Memory Allocation**: Allocate memory with canaries and encryption
- **Memory Protection**: Set memory permissions and lock memory to prevent swapping
- **Pattern Matching**: Search for specific patterns in memory regions
- **Sensitive Data Scanning**: Detect common sensitive data formats (credit cards, passwords, keys)
- **Anti-Forensics**: Apply obfuscation and encryption to prevent memory forensics
- **Process Memory Access**: Access and modify memory in other processes
- **Memory Integrity Verification**: Verify memory hasn't been tampered with

## API Reference

### Initialization

```c
bool memory_manipulation_init(const MemoryOptions* options);
void memory_manipulation_cleanup(void);
```

Initialize and clean up the memory manipulation module. The `MemoryOptions` struct allows configuring:
- `protection_level`: Level of protection (NONE, STANDARD, HIGH, EXTREME)
- `secure_heap_size`: Size of the secure heap for HIGH or EXTREME protection
- `enable_anti_forensics`: Enable anti-forensics techniques
- `detect_debugger`: Enable detection of attached debuggers

### Secure Memory Allocation

```c
void* mem_alloc_secure(size_t size, const MemAllocOptions* options);
void mem_free_secure(void* ptr, size_t size);
```

Allocate and free secure memory regions. `MemAllocOptions` allows configuring:
- `protection`: Memory protection flags (READ, WRITE, EXEC, NONE)
- `use_canaries`: Add canary values to detect buffer overflows
- `encrypt_data`: Encrypt data at rest in memory
- `prevent_dumps`: Prevent memory from being included in crash dumps

### Memory Scanning

```c
bool mem_find_pattern(const void* memory, size_t size, const void* pattern, size_t pattern_size, void** result);
int mem_scan_sensitive_data(const void* memory, size_t size, uint32_t flags, SensitiveDataCallback callback);
```

Search for patterns or sensitive data in memory regions. Sensitive data scanning supports:
- Credit card numbers
- Passwords
- SSH keys
- PGP keys
- Certificates

### Memory Protection

```c
bool mem_protect(void* memory, size_t size, int protection);
bool mem_lock(void* memory, size_t size);
bool mem_unlock(void* memory, size_t size);
bool mem_verify_integrity(void* memory, size_t size);
```

Apply protection measures to memory regions, lock memory to prevent swapping, and verify memory integrity.

### Anti-Forensics

```c
bool mem_apply_anti_forensics(void* memory, size_t size, const MemAntiForensicsOptions* options);
```

Apply anti-forensics measures to prevent memory forensic analysis. `MemAntiForensicsOptions` allows configuring:
- `encrypt_data`: Encrypt the memory contents
- `add_obfuscation`: Add obfuscation techniques
- `fragment_memory`: Fragment memory to prevent contiguous analysis

### Process Memory Access

```c
bool process_read_memory(uint32_t pid, const void* remote_address, void* buffer, size_t size);
bool process_write_memory(uint32_t pid, void* remote_address, const void* buffer, size_t size);
bool process_find_module(uint32_t pid, const char* module_name, void** base_address, size_t* module_size);
bool process_query_memory(uint32_t pid, const void* address, int* protection, size_t* size);
bool process_protect_memory(uint32_t pid, void* address, size_t size, int protection);
```

Functions for interacting with memory in other processes.

## Usage Examples

### Protecting Sensitive Data

```c
// Initialize the module
MemoryOptions options = {
    .protection_level = MEM_PROTECTION_HIGH,
    .secure_heap_size = 1024 * 1024,
    .enable_anti_forensics = true,
    .detect_debugger = true
};
memory_manipulation_init(&options);

// Allocate secure memory
MemAllocOptions alloc_options = {
    .protection = MEM_PROT_READ | MEM_PROT_WRITE,
    .use_canaries = true,
    .encrypt_data = true,
    .prevent_dumps = true
};
void* secure_memory = mem_alloc_secure(1024, &alloc_options);

// Use the memory for sensitive data
strcpy(secure_memory, "Credit card: 4111-1111-1111-1111");

// Lock the memory to prevent swapping
mem_lock(secure_memory, 1024);

// When done, free the memory securely
mem_free_secure(secure_memory, 1024);

// Clean up
memory_manipulation_cleanup();
```

### Scanning for Sensitive Data

```c
// Callback function for sensitive data detection
bool handle_sensitive_data(const SensitiveDataInfo* info) {
    printf("Found sensitive data of type %d\n", info->type);
    
    // Take action based on the data found
    switch (info->type) {
        case MEM_DATA_CREDIT_CARD:
            // Handle credit card data
            break;
        case MEM_DATA_PASSWORD:
            // Handle password data
            break;
        // Handle other types
    }
    
    return true; // Continue scanning
}

// Scan memory for sensitive data
int found = mem_scan_sensitive_data(memory, size, MEM_SCAN_ALL, handle_sensitive_data);
```

## Building

To build the memory manipulation module:

```bash
# Using make
make mem_lib

# Run tests
make memtest
```

## Security Considerations

- This module deals with sensitive data and should be used with care
- Ensure proper access controls are in place for any process using these functions
- Anti-forensics capabilities should only be used when justified by security requirements
- Memory manipulation of other processes may require elevated privileges and should be restricted

## Platform Support

- Windows: Full support
- Linux: Most features supported
- macOS: Basic features supported
- Other UNIX-like systems: Varies by platform

## Dependencies

- Standard C library
- Platform-specific libraries:
  - Windows: psapi.dll
  - Linux: pthread, dl

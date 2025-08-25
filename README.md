# Heap Memory Allocator

## Overview

This project implements a custom heap memory allocator in C, providing `balloc` (block allocate) and `bfree` (block free) functions similar to standard `malloc` and `free`. The allocator uses a **best-fit placement policy** and supports **immediate coalescing** of adjacent free blocks.

## Files

- **p3Heap.c**: Main implementation file containing the memory allocator functions
- **p3Heap.h**: Header file with function prototypes
- **testXXX.c**: Various test files to verify functionality
- **Makefile**: Build configuration
- **p3Heap.o**: Compiled object file
- **libHeap.so**: Shared library version

## Key Features

### Data Structure
- **blockHeader**: Structure that serves as both header and footer for memory blocks
  - `size_status` field stores both size and status information using bit masking:
    - **Bit 0**: Allocation status (0 = free, 1 = allocated)
    - **Bit 1**: Previous block status (0 = free, 1 = allocated)

### Functions

#### `void* balloc(int size)`
Allocates a block of memory of the requested size.

**Algorithm:**
1. Validates requested size (> 0 bytes)
2. Calculates block size (including header, rounded to multiple of 8)
3. Uses best-fit policy to find the smallest sufficient free block
4. Splits blocks if sufficient leftover space (≥ 8 bytes)
5. Returns pointer to payload (after header)

#### `int bfree(void *ptr)`
Frees a previously allocated block (implementation pending).

**Planned features:**
- Validation of pointer
- Immediate coalescing of adjacent free blocks
- Header/footer updates

#### `int init_heap(int sizeOfRegion)`
Initializes the heap memory space.

**Features:**
- Allocates memory using `mmap`
- Sets up initial free block
- Establishes end mark for heap termination
- Ensures double-word alignment

#### `void disp_heap()`
Debug function that displays heap structure and block information.

## Implementation Details

### Memory Alignment
- All blocks are aligned to 8-byte boundaries
- First block starts after 4-byte padding for alignment
- Block sizes include header and are multiples of 8

### Block Structure
**Allocated blocks:**
- Header only (with size and status bits)

**Free blocks:**
- Header (with size and status bits)
- Footer (with size only)

### Status Encoding
Examples of `size_status` values:
- Free block (24 bytes) with previous block free: `24` (binary: `00011000`)
- Free block (24 bytes) with previous block allocated: `26` (binary: `00011010`)
- Allocated block (24 bytes) with previous block free: `25` (binary: `00011001`)
- Allocated block (24 bytes) with previous block allocated: `27` (binary: `00011011`)

## Usage

1. Initialize the heap:
   ```c
   init_heap(1024); // Initialize with 1KB heap space
   ```

2. Allocate memory:
   ```c
   int* data = (int*) balloc(sizeof(int) * 10);
   ```

3. Free memory (when implemented):
   ```c
   bfree(data);
   ```

4. Debug heap structure:
   ```c
   disp_heap();
   ```

## Building

Compile using the provided Makefile:
```bash
make
```

This will generate:
- `p3Heap.o` - Object file
- `libHeap.so` - Shared library

## Testing

Run the provided test files to verify functionality:
```bash
make test
```

## Notes

- The allocator uses a best-fit policy to minimize fragmentation
- Immediate coalescing helps reduce external fragmentation
- The implementation is thread-unsafe (single-threaded use only)
- All blocks include overhead for headers/footers

## Future Enhancements

- Complete `bfree` implementation with coalescing
- Add thread safety mechanisms
- Implement reallocation functionality
- Add additional debugging features

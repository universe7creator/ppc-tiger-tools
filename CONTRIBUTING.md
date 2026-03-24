# Contributing to ppc-tiger-tools

Thank you for your interest in contributing to ppc-tiger-tools! This project preserves and enhances development tools for PowerPC Mac OS X Tiger (10.4), keeping classic Apple hardware productive.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Environment](#development-environment)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Pull Request Process](#pull-request-process)
- [Release Process](#release-process)

## Code of Conduct

This project adheres to a code of conduct that prioritizes:
- **Preservation**: Respecting vintage hardware and software
- **Compatibility**: Maintaining Tiger/PowerPC support
- **Documentation**: Clear explanations for retro-computing workflows
- **Inclusivity**: Welcoming both vintage enthusiasts and newcomers

## Getting Started

### Prerequisites

To contribute effectively, you'll need:

1. **PowerPC Mac** (G3, G4, or G5) OR emulator setup
2. **Mac OS X Tiger 10.4** (preferred) or compatible environment
3. **Xcode 2.5** (available from Apple Developer Connection archives)
4. **Git 1.5+** (for version control)
5. **Basic knowledge** of PowerPC assembly and Tiger APIs

### Emulator Setup (No Hardware Required)

If you don't have physical PowerPC hardware:

```bash
# Using QEMU (recommended)
qemu-system-ppc -M mac99 -cpu g4 \
  -m 1024 \
  -hda tiger.dmg \
  -netdev user,id=net0 \
  -device sungem,netdev=net0
```

For detailed emulation setup, see [QEMU PowerPC Mac](https://wiki.qemu.org/Documentation/Platforms/PowerPC).

## Development Environment

### Project Structure

```
ppc-tiger-tools/
├── bin/              # Compiled binaries
├── src/              # Source code
├── include/          # Header files
├── lib/              # Static libraries
├── tools/            # Development utilities
├── docs/             # Documentation
└── tests/            # Test suite
```

### Building from Source

```bash
# Clone the repository
git clone https://github.com/Scottcjn/ppc-tiger-tools.git
cd ppc-tiger-tools

# Set up build environment
export MACOSX_DEPLOYMENT_TARGET=10.4
export CFLAGS="-arch ppc -isysroot /Developer/SDKs/MacOSX10.4u.sdk"

# Build
make clean
make all

# Run tests
make test
```

### Tiger-Specific Considerations

- **SDK Path**: `/Developer/SDKs/MacOSX10.4u.sdk`
- **Architecture**: PowerPC only (`-arch ppc`)
- **Deployment Target**: 10.4 minimum
- **Compiler**: GCC 4.0 (included with Xcode 2.5)

## How to Contribute

### Reporting Bugs

When reporting bugs, please include:

1. **Hardware model** (e.g., Power Mac G4 Dual 1.25GHz)
2. **Mac OS X version** (e.g., 10.4.11)
3. **Xcode version** (e.g., 2.5)
4. **Steps to reproduce**
5. **Expected vs actual behavior**
6. **Error messages** (copy-paste or screenshot)

### Suggesting Features

Feature suggestions are welcome! Please:

1. Check existing issues first
2. Explain the use case for Tiger/PowerPC
3. Describe the proposed solution
4. Note any compatibility concerns

### Contributing Code

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Make your changes
4. Test on Tiger/PowerPC or emulator
5. Commit with clear messages
6. Push and submit a Pull Request

## Coding Standards

### C/C++ Style

Follow these guidelines for Tiger compatibility:

```c
// Use classic C89/C++98 for maximum compatibility
void process_data(const char *input, size_t len)
{
    // Avoid C99 features not supported in GCC 4.0
    int i;
    for (i = 0; i < len; i++) {
        // Process each byte
    }
}

// PowerPC-specific optimizations
#ifdef __ppc__
    // Use AltiVec for vector operations
    #include <altivec.h>
#endif
```

### PowerPC Assembly

When writing assembly:

```asm
# Use standard PowerPC mnemonics
# G3/G4: 32-bit, G5: 64-bit capable

    .text
    .globl _fast_memcpy
_fast_memcpy:
    # r3 = dest, r4 = src, r5 = len
    cmplwi  r5, 0
    beqlr                   # Return if len == 0
    
    # Use AltiVec for large copies on G4/G5
    cmpwi   r5, 16
    blt     byte_copy
    
    # AltiVec optimized path
    # ... vector operations ...
    
byte_copy:
    lbz     r6, 0(r4)
    stb     r6, 0(r3)
    addi    r3, r3, 1
    addi    r4, r4, 1
    subi    r5, r5, 1
    cmpwi   r5, 0
    bne     byte_copy
    blr
```

### Tiger API Usage

```objc
// Use Tiger-compatible APIs only
#import <Cocoa/Cocoa.h>

@implementation MyTigerClass

- (void)compatibleMethod {
    // Avoid Leopard+ APIs (10.5+)
    // Check NSAppKitVersionNumber if needed
    
    if (NSAppKitVersionNumber >= NSAppKitVersionNumber10_4) {
        // Safe to use Tiger APIs
    }
}

@end
```

### Code Formatting

- **Indentation**: 4 spaces (no tabs)
- **Line length**: 80 characters maximum
- **Braces**: K&R style (opening brace on same line)
- **Comments**: Use `/* */` for multi-line, `//` for single-line

## Testing

### Test Categories

1. **Unit Tests**: Individual function testing
2. **Integration Tests**: Component interaction
3. **Hardware Tests**: Physical PowerPC validation
4. **Emulator Tests**: QEMU validation

### Running Tests

```bash
# Run all tests
make test

# Run specific test category
make test-unit
make test-integration

# Run with verbose output
make test VERBOSE=1
```

### Hardware Testing

If you have physical PowerPC hardware:

```bash
# Run hardware-specific tests
make test-hardware

# Test on specific models
./test_g4.sh    # Power Mac G4
./test_g5.sh    # Power Mac G5
./test_ibg.sh   # iBook G4
```

### Emulator Testing

For contributors without hardware:

```bash
# Start QEMU with Tiger
./scripts/start_qemu.sh

# Run tests in emulator
make test-emulator
```

## Pull Request Process

1. **Update documentation** if adding features
2. **Add tests** for new functionality
3. **Ensure tests pass** on Tiger/PowerPC
4. **Update CHANGELOG.md** with your changes
5. **Reference any related issues**

### PR Checklist

- [ ] Code compiles on Tiger with `-arch ppc`
- [ ] Tests pass (unit + integration)
- [ ] Documentation updated
- [ ] CHANGELOG.md updated
- [ ] No hardcoded paths
- [ ] Tiger-compatible APIs only

### Review Process

Maintainers will review for:
- Tiger/PowerPC compatibility
- Code quality and style
- Test coverage
- Documentation completeness

## Release Process

Releases follow semantic versioning:

- **MAJOR**: Breaking changes to API/ABI
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, backward compatible

### Version Numbers

Format: `MAJOR.MINOR.PATCH`

Example: `1.2.3` (Major 1, Minor 2, Patch 3)

## Questions?

- Open an issue for questions
- Join discussions in existing issues
- Check the [wiki](../../wiki) for additional documentation

## Recognition

Contributors will be:
- Listed in CONTRIBUTORS.md
- Mentioned in release notes
- Credited in documentation

Thank you for helping preserve PowerPC Tiger development!

---

**Note**: This project maintains compatibility with Mac OS X Tiger 10.4 on PowerPC architecture. All contributions should respect this constraint.

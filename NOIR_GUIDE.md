# Noir: A Programming Language for Zero Knowledge Proofs

**Noir** is a domain-specific programming language developed by **Aztec** for creating zero knowledge proofs (ZKPs) easily and efficiently. This guide provides a comprehensive overview of Noir and its capabilities.

---

## Table of Contents

1. [What is Noir?](#what-is-noir)
2. [Why Noir?](#why-noir)
3. [Zero Knowledge Proofs Basics](#zero-knowledge-proofs-basics)
4. [Noir Language Features](#noir-language-features)
5. [Project Structure](#project-structure)
6. [Installation & Setup](#installation--setup)
7. [Getting Started](#getting-started)
8. [Resources & Documentation](#resources--documentation)

---

## What is Noir?

**Noir** is a domain-specific language (DSL) designed specifically for writing zero knowledge circuits. It abstracts away the complexity of cryptographic primitives and allows developers to focus on circuit logic without deep knowledge of cryptography.

### Key Characteristics

- **Designed for ZK**: Built from the ground up for zero knowledge proof generation
- **High-Level Syntax**: Similar to Rust, making it accessible to developers familiar with modern programming languages
- **Backend Agnostic**: Circuits can be compiled to different proving backends (Barretenberg, etc.)
- **Efficient**: Produces optimized arithmetic circuits for proof generation
- **Developer-Friendly**: Clear syntax and comprehensive tooling

### Developed By

**Aztec Network** - A privacy-focused Layer 2 solution on Ethereum that uses advanced cryptography to enable private transactions and smart contracts.

---

## Why Noir?

### Problems Noir Solves

1. **Complexity Reduction**: Eliminates the need to manually write arithmetic circuits
2. **Accessibility**: Non-cryptographers can write ZK circuits
3. **Development Speed**: Faster circuit development compared to low-level languages
4. **Maintainability**: Clear, readable code that's easier to audit and maintain
5. **Standardization**: Unified approach to circuit development across projects

### Use Cases

- **Privacy-Preserving Applications**: Build private smart contracts and dApps
- **Scalability Solutions**: Create off-chain computations that can be verified on-chain
- **Authentication Systems**: Prove identity or credentials without revealing sensitive information
- **Financial Privacy**: Implement private transactions and confidential computations
- **Proving Compliance**: Demonstrate adherence to rules without exposing underlying data

---

## Zero Knowledge Proofs Basics

### What are Zero Knowledge Proofs (ZKPs)?

A zero knowledge proof is a cryptographic method that allows one party (the **prover**) to convince another party (the **verifier**) that a statement is true, without revealing any information beyond the validity of the statement itself.

### The Three Properties

1. **Completeness**: If the statement is true, an honest prover can always convince the verifier
2. **Soundness**: If the statement is false, no dishonest prover can convince the verifier
3. **Zero Knowledge**: The verifier learns nothing except that the statement is true

### Key Participants

| Role | Description |
|------|-------------|
| **Prover** | Creates the proof using private information (witness) |
| **Verifier** | Validates the proof without seeing private information |
| **Witness** | Private data used by the prover to generate the proof |
| **Public Input** | Information visible to both prover and verifier |

### Why ZKPs Matter

- **Privacy**: Prove facts without revealing underlying data
- **Scalability**: Enable off-chain computation with on-chain verification
- **Security**: Cryptographically secure proofs that cannot be forged
- **Efficiency**: Faster verification than re-computation

---

## Noir Language Features

### 1. Functions

```noir
fn add(x: Field, y: Field) -> Field {
    x + y
}
```

- Similar syntax to Rust
- Type annotations required
- Return type specified with `->`

### 2. Variables & Type System

```noir
fn main() {
    let x: Field = 42;           // Field type
    let y: u32 = 100;            // 32-bit unsigned integer
    let is_valid: bool = true;   // Boolean
    let name: str = "Noir";      // String
    let arr: [u8; 3] = [1, 2, 3]; // Array
}
```

**Supported Types**:
- `Field`: Native field element (base type)
- `u8, u16, u32, u64`: Unsigned integers
- `i8, i16, i32, i64`: Signed integers
- `bool`: Boolean values
- `str`: Fixed-length strings
- Arrays and Slices

### 3. Input Visibility

```noir
fn main(x: Field, y: pub Field) {
    // x is private (only prover knows)
    // y is public (both prover and verifier know)
}
```

- **Private inputs**: Default behavior, known only to prover
- **Public inputs**: Marked with `pub`, known to both prover and verifier

### 4. Assertions & Constraints

```noir
fn main(x: Field) {
    assert(x != 0);           // Simple assertion
    assert(x > 10 && x < 100); // Complex conditions
}
```

- Assert statements create circuit constraints
- Failed assertions mean the proof cannot be generated
- Core mechanism for defining circuit logic

### 5. Testing

```noir
#[test]
fn test_example() {
    add(2, 3);
}
```

- Use `#[test]` attribute for test functions
- Run tests to validate circuit logic
- Helps catch bugs during development

### 6. Control Flow

```noir
fn is_positive(x: Field) -> bool {
    if x > 0 {
        true
    } else {
        false
    }
}
```

- `if/else` statements
- `for` loops for iteration
- Pattern matching support

### 7. Built-in Functions & Libraries

Noir provides numerous built-in functions:

- **Hashing**: SHA256, Keccak256, Blake2s
- **Cryptography**: ECDSA, Schnorr signatures
- **Field Operations**: Arithmetic operations optimized for ZK
- **Data Structures**: Arrays, tuples, structs

---

## Project Structure

A typical Noir project has the following structure:

```
my_circuit/
├── Nargo.toml          # Project manifest
├── Prover.toml         # Input values for testing
├── README.md           # Project documentation
├── src/
│   ├── main.nr         # Main circuit entry point
│   └── lib.nr          # Library code (optional)
├── tests/
│   └── integration.nr  # Integration tests (optional)
└── target/
    ├── simple_circuit.json      # Compiled circuit
    ├── simple_circuit.gz        # Witness file
    ├── vk                       # Verification key
    ├── proof                    # Generated proof
    └── public_inputs            # Public inputs
```

### Nargo.toml

```toml
[package]
name = "my_circuit"
type = "bin"
authors = ["Your Name"]
compiler_version = "0.21.0"

[dependencies]
```

- `name`: Project name
- `type`: Project type (bin for binary, lib for library)
- `compiler_version`: Noir compiler version
- `dependencies`: External libraries

---

## Installation & Setup

### Prerequisites

- **Rust**: Required for building Noir (install from https://rustup.rs/)
- **Node.js**: Optional, for JavaScript integration

### Installing Nargo

**Using Noirup** (recommended):

```bash
curl -L https://raw.githubusercontent.com/noir-lang/noirup/main/install | bash
noirup
```

**Verify Installation**:

```bash
nargo --version
```

### Creating a New Project

```bash
nargo new my_circuit
cd my_circuit
```

This creates a new Noir project with:
- `Nargo.toml` manifest
- `src/main.nr` entry point
- `Prover.toml` for inputs

---

## Getting Started

### Step 1: Write Your Circuit

Edit `src/main.nr`:

```noir
fn main(x: Field, y: pub Field) {
    assert(x != y);
}
```

### Step 2: Define Inputs

Edit `Prover.toml`:

```toml
x = "1"
y = "2"
```

### Step 3: Check & Compile

```bash
# Validate circuit structure
nargo check

# Compile to ACIR
nargo compile

# Execute and generate witness
nargo execute
```

### Step 4: Generate Proof

```bash
# Generate verification key
bb write_vk -b ./target/simple_circuit.json -o ./target

# Generate proof
bb prove -b ./target/simple_circuit.json -w ./target/simple_circuit.gz -o ./target
```

### Step 5: Verify Proof

```bash
bb verify -k ./target/vk -p ./target/proof
```

---

## Development Workflow

### 1. Circuit Development

```bash
# Check syntax and structure
nargo check

# Compile circuit
nargo compile

# Run tests
nargo test
```

### 2. Proof Generation

```bash
# Execute to generate witness
nargo execute

# Create verification key
bb write_vk -b ./target/simple_circuit.json -o ./target

# Generate proof
bb prove -b ./target/simple_circuit.json -w ./target/simple_circuit.gz -o ./target
```

### 3. Proof Verification

```bash
# Verify proof without re-executing circuit
bb verify -k ./target/vk -p ./target/proof
```

---

## Common Patterns

### Pattern 1: Range Proofs

Prove that a value is within a certain range without revealing the exact value:

```noir
fn main(x: Field, min: pub Field, max: pub Field) {
    assert(x > min);
    assert(x < max);
}
```

### Pattern 2: Equality Proofs

Prove knowledge of a value that equals a hash without revealing the value:

```noir
fn main(x: Field, hash: pub Field) {
    assert(sha256(x) == hash);
}
```

### Pattern 3: Complex Constraints

Build circuits with multiple constraints:

```noir
fn main(a: Field, b: Field, result: pub Field) {
    assert(a * b == result);
    assert(a != 0);
    assert(b != 0);
}
```

---

## Proving Backends

Noir is backend-agnostic and supports multiple proving systems:

### Barretenberg (Current Default)

- **Scheme**: ultra_honk
- **Field**: BN254
- **Performance**: Optimized for production use
- **Community**: Active development and support

### Other Backends

- **Halo 2**: For different elliptic curves
- **Plonk**: Alternative proving scheme
- **Custom Backends**: Can be implemented as needed

---

## Best Practices

### 1. Input Validation

```noir
fn main(x: pub Field) {
    assert(x > 0);
    assert(x < 1000);
}
```

### 2. Clear Variable Names

```noir
fn verify_age(user_age: Field, min_age: pub Field) {
    assert(user_age >= min_age);
}
```

### 3. Modular Code

```noir
// Separate concerns into functions
fn is_valid_range(value: Field, min: Field, max: Field) -> bool {
    (value >= min) & (value <= max)
}
```

### 4. Testing

```noir
#[test]
fn test_valid_case() {
    verify_age(25, 18);
}

#[test]
#[should_fail]
fn test_invalid_case() {
    verify_age(15, 18);
}
```

### 5. Documentation

```noir
// Verify that the prover knows a value whose hash matches the public input
fn main(secret: Field, hash: pub Field) {
    assert(sha256(secret) == hash);
}
```

---

## Resources & Documentation

### Official Resources

- **Noir Documentation**: https://noir-lang.org/docs/
- **Aztec Network**: https://aztec.network/
- **GitHub Repository**: https://github.com/noir-lang/noir
- **Community Forum**: https://forum.aztec.network/

### Learning Materials

- **Noir Book**: Comprehensive guide to Noir programming
- **Example Circuits**: Repository of circuit examples
- **Tutorials**: Step-by-step guides for beginners
- **Developer Workshops**: Video tutorials and webinars

### Community

- **Discord**: Aztec Network and Noir community
- **GitHub Discussions**: Ask questions and share ideas
- **Twitter/X**: Follow for updates and announcements

---

## Comparison with Other Languages

| Feature | Noir | Circom | ZoKrates |
|---------|------|--------|----------|
| **Language Style** | Rust-like | JavaScript-like | Custom |
| **Learning Curve** | Moderate | Easy | Steep |
| **Performance** | High | Medium | Medium |
| **Ecosystem** | Growing | Mature | Mature |
| **Backend Support** | Multiple | Limited | Limited |
| **Community** | Active | Large | Medium |

---

## Limitations & Considerations

### Current Limitations

1. **Performance**: Proof generation can be slow for complex circuits
2. **Memory**: Large circuits require significant memory
3. **Circuit Size**: Very large circuits may hit practical limits
4. **Field Arithmetic**: Computation happens in a finite field, not standard integers

### Development Considerations

1. **Debugging**: Limited debugging tools compared to traditional languages
2. **Circuit Optimization**: Requires understanding of circuit constraints
3. **Backend Knowledge**: Some understanding of cryptography helpful
4. **Testing**: Testing approach differs from traditional software

---

## Future of Noir

### Upcoming Features

- Enhanced debugging tools
- Improved performance optimizations
- Expanded standard library
- Better integration with smart contracts
- Advanced compiler optimizations

### Roadmap Areas

- Cross-platform compatibility
- Better error messages
- IDE plugins and extensions
- More backend support
- Community-contributed libraries

---

## Conclusion

**Noir** makes zero knowledge proof development accessible to programmers without deep cryptographic expertise. By abstracting away complexity while maintaining flexibility, Noir enables developers to build privacy-preserving and scalable applications efficiently.

Whether you're building privacy applications, scaling solutions, or exploring ZKPs, Noir provides the tools and ecosystem to bring your ideas to life.

---

## Next Steps

1. **Install Noir**: Follow the installation guide above
2. **Create Your First Circuit**: Start with simple examples
3. **Join the Community**: Engage with other developers
4. **Explore Examples**: Study existing projects
5. **Build & Deploy**: Create your own ZK applications

---

*For more information and updates, visit [noir-lang.org](https://noir-lang.org/)*

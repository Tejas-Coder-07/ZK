# Noir Basics: My First Zero Knowledge Proof Circuit


---

## Project Overview

This is my first circuit project using **Noir**, a domain-specific language for writing zero knowledge proofs. The project demonstrates the fundamental concepts and the complete workflow from circuit development to proof generation and verification.

### Key Technologies
- **Language**: Noir (`.nr` file extension)
- **Proving Backend**: Barretenberg (ultra_honk scheme)
- **Project Structure**: Binary crate

---

## Understanding the Code

### Basic Circuit Example

```noir
fn main(x: Field, y: pub Field) {
    assert(x != y);
}
```

This simple circuit contains:
- **Input Variables**: `x` and `y`
- **Logic**: Assert that `x` is not equal to `y`

### Input Visibility

#### Private Inputs (Default)
- Inputs without the `pub` keyword are **private**
- Known only to the **prover** (the person generating the proof)
- Not visible to the verifier

**Example**: `x: Field` is private

#### Public Inputs
- Inputs with the `pub` keyword are **public**
- Known to both the **prover** and the **verifier**
- Part of the public statement being proved

**Example**: `y: pub Field` is public

### The Assert Function

```noir
assert(x != y);
```

- The `assert` function verifies that a given condition is `true`
- In this circuit, it ensures that the private input `x` is not equal to the public input `y`
- If the assertion fails, the circuit proof cannot be generated

---

## Variable Types in Noir

### 1. **Field**
- An integer with a maximum value equal to the **size field modulus** (similar to `uint256`)
- The **native field type** of the proving backend (Barretenberg)
- Default type when no explicit type is specified
- Used for arithmetic operations

**Example**:
```noir
fn main(x: Field, y: pub Field) {
    assert(x != y);
}
```

### 2. **Integers**
- Explicitly typed integer variables
- Used when specific bit-width constraints are needed
- Require type annotation
- Can be used in arithmetic operations

**Example**:
```noir
let x: u8 = 1;   // 8-bit unsigned integer
let y: u32 = 42; // 32-bit unsigned integer
let z: u64 = 100; // 64-bit unsigned integer
```

### 3. **Boolean**
- Represents true/false values
- Used in conditional logic and assertions

**Example**:
```noir
let is_valid: bool = true;
```

### 4. **Strings**
- Character sequences with fixed length
- Used for textual data

### 5. **Arrays**
- Fixed-size collections of elements
- All elements must be of the same type
- Accessed by index

### 6. **Slices**
- Dynamic-size views into arrays
- Allow flexible array handling

---

## Testing in Noir

### Understanding Test Functions

```noir
#[test]
fn test_main() {
    main(x: 1, y: 2);
}
```

- **`#[test]` attribute**: Marks the next function as a test
- **Function purpose**: Validates circuit logic without full compilation
- **Test parameters**: Pass specific values to the circuit's main function
- **Success condition**: If the test passes, it means the circuit will compile correctly

**Example Breakdown**:
```noir
#[test]
fn test_example() {
    main(x: 1, y: 2);  // Pass x=1, y=2
                        // Assert checks: 1 != 2 ✓ (passes)
}
```

If `x` and `y` were equal, the test would fail:
```noir
#[test]
fn test_fails() {
    main(x: 1, y: 1);  // Assert checks: 1 != 1 ✗ (fails)
}
```

---

## Project Structure & Files

### Nargo.toml
- **Purpose**: Manifest file for Noir projects (similar to `Cargo.toml` in Rust)
- **Contains**: Project metadata, dependencies, and configuration
- **Required**: Every Noir project must have this file

### src/main.nr
- **Purpose**: Entry point for the binary crate
- **Contains**: `fn main()` function and test functions
- **File Extension**: `.nr` is the standard Noir file extension

### Project Type: Binary Crate
- Compiles to an **executable for ZK proofs**
- Each circuit is a self-contained unit
- Can be used to generate proofs independently

---

## Complete Workflow: From Circuit to Proof

### Step 1: Check Circuit Structure

```bash
nargo check
```

**Purpose**:
- Validates the circuit structure without compilation or execution
- Checks for syntax errors and basic structure validity
- Generates `Prover.toml` file for input variables

**Output**:
- `Prover.toml`: Template file where you define input values

---

### Step 2: Compile to ACIR

```bash
nargo compile
```

**Purpose**:
- Compiles the Noir circuit into **ACIR** (Arithmetic Circuit IR)
- Converts circuit logic into arithmetic circuit representation

**Output**:
- Prepares the circuit for execution and backend processing

---

### Step 3: Execute & Generate Witness

```bash
nargo execute
```

**Purpose**:
- Compiles and executes all circuit steps
- Solves the witness (the satisfying assignment for all signals)
- Saves the witness to the `/target` directory

**Output Files**:

#### simple_circuit.json (Compiled Circuit)
- Contains the **ACIR** (Arithmetic Circuit Intermediate Representation)
- Contains the **ABI** (Application Binary Interface)
- Represents the circuit bytecode
- Used to create the proof

#### simple_circuit.gz (Witness File)
- Compressed witness file
- Contains all variable assignments that satisfy the circuit
- Required for proof generation

---

### Step 4: Generate Verification Key

```bash
bb write_vk -b ./target/simple_circuit.json -o ./target
```

**Purpose**:
- Generates the **verification key (VK)** from the compiled circuit
- The VK is used to verify proofs off-chain
- Constructs the circuit quickly with invalid witnesses
- Expands ACIR black box functions

**Output**:
- `vk` file: Verification key stored in the target directory

---

### Step 5: Generate the Proof

```bash
bb prove -b ./target/simple_circuit.json -w ./target/simple_circuit.gz -o ./target
```

**Command Breakdown**:
- `-b` (bytecode): Path to compiled circuit (`simple_circuit.json`)
- `-w` (witness): Path to witness file (`simple_circuit.gz`)
- `-o` (output): Output directory for proof files (`.`)

**Purpose**:
- Uses the **Barretenberg backend** to create a ZK proof
- Combines the compiled circuit and witness to generate cryptographic proof

**Backend Information**:
- **Scheme**: ultra_honk (current Barretenberg proving scheme)
- **Memory Utilization**: Optimized multi-threaded processing (typically 10 threads)

**Output Files**:
- `proof`: The generated zero knowledge proof
- `public_inputs`: Public inputs used in the proof generation

---

## Proof Verification

### Verify the Generated Proof

```bash
bb verify -k ./target/vk -p ./target/proof
```

**Command Breakdown**:
- `-k` (key): Path to verification key file
- `-p` (proof): Path to proof file

**Purpose**:
- Verifies the proof **off-chain** without executing the circuit
- Checks if the proof matches the circuit logic
- Performs validation before on-chain verification

**Verification Process**:
1. Takes the verification key (`vk`)
2. Takes the proof (`proof`)
3. Checks cryptographic validity
4. Returns success if proof is valid, failure otherwise

**Use Cases**:
- Off-chain verification before smart contract interaction
- Validation in backend services
- Pre-flight checks before deploying to blockchain

---

## Generated Files Summary

After the complete workflow, your `/target` directory contains:

| File | Purpose |
|------|---------|
| `simple_circuit.json` | Compiled circuit bytecode (ACIR + ABI) |
| `simple_circuit.gz` | Witness file (compressed) |
| `vk` | Verification key for proof validation |
| `proof` | Generated zero knowledge proof |
| `public_inputs` | Public inputs used in the proof |

---

## Complete Command Sequence

```bash
# 1. Check circuit structure
nargo check

# 2. Compile to ACIR
nargo compile

# 3. Execute to generate witness
nargo execute

# 4. Generate verification key
bb write_vk -b ./target/simple_circuit.json -o ./target

# 5. Generate proof
bb prove -b ./target/simple_circuit.json -w ./target/simple_circuit.gz -o ./target

# 6. Verify the proof (optional but recommended)
bb verify -k ./target/vk -p ./target/proof
```

---

## Key Learnings

### About Nargo
- **Nargo** is the command-line tool for Noir development
- Handles compilation, execution, and circuit management
- Creates configuration files automatically

### About Binary Crates
- This project is a **binary crate** (not a library)
- Compiles to an executable for generating zero knowledge proofs
- Each circuit operates independently

### About Libraries
- Noir supports library crates for code reuse
- Libraries allow modular circuit design
- Will be explored in future projects

### About File Extensions
- `.nr` is the standard Noir file extension
- All Noir source code uses this extension

### About the Entry Point
- `src/main.nr` serves as the entry point for the binary crate
- Contains the `fn main()` function and test functions
- Defines the circuit logic

---

## Next Steps

As I continue this learning journey, I will:
- ✅ Understand basic circuit structure and variable types
- ✅ Learn the complete workflow from development to verification
- ✅ Explore more complex circuit logic and constraints
- 🔲 Create library crates for code reuse
- 🔲 Build more sophisticated ZK circuits
- 🔲 Integrate circuits with smart contracts
- 🔲 Explore advanced Noir features and optimizations

---

*This document serves as a comprehensive reference for Noir basics and will be updated as I progress through more advanced topics.*

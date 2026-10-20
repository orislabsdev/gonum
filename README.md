# gonum

[![Go Reference](https://pkg.go.dev/badge/github.com/orislabsdev/gonum.svg)](https://pkg.go.dev/github.com/orislabsdev/gonum)
[![Go Report Card](https://goreportcard.com/badge/github.com/orislabsdev/gonum)](https://goreportcard.com/report/github.com/orislabsdev/gonum)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Go Version](https://img.shields.io/badge/Go-%3E%3D%201.22-00ADD8?logo=go)](https://go.dev/)

`gonum` is an arbitrary-precision decimal arithmetic and high-precision mathematics library written in pure Go with **zero external dependencies**.

It is engineered for financial calculations, scientific computing, blockchain systems, and any application where IEEE-754 floating-point inaccuracies (such as `0.1 + 0.2 != 0.3`) are unacceptable.

---

## Table of Contents

- [Features](#features)
- [Why gonum?](#why-gonum)
- [Installation](#installation)
- [Quick Start](#quick-start)
  - [Basic Arithmetic](#basic-arithmetic)
  - [Financial Calculations & Rounding](#financial-calculations--rounding)
  - [Scientific Functions & Constants](#scientific-functions--constants)
  - [Trigonometric Functions](#trigonometric-functions)
  - [JSON Serialization](#json-serialization)
  - [Binary Conversions](#binary-conversions)
- [Architecture & Design](#architecture--design)
- [API Reference](#api-reference)
  - [Constructors](#constructors)
  - [Arithmetic Operations](#arithmetic-operations)
  - [Comparison & Predicates](#comparison--predicates)
  - [Mathematical & Rounding](#mathematical--rounding)
  - [Logarithms & Exponents](#logarithms--exponents)
  - [Trigonometric & Hyperbolic](#trigonometric--hyperbolic)
  - [Mathematical Constants](#mathematical-constants)
  - [Formatting & Stringification](#formatting--stringification)
- [Running Tests](#running-tests)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **Arbitrary Precision**: No loss of precision, no bit overflow, and no IEEE-754 rounding surprises.
- **Pure Go & Zero Dependencies**: Lightweight, cross-platform, relies only on the Go standard library.
- **Rich Arithmetic Operations**: Addition, subtraction, multiplication, division with custom precision, integer modulo, quotient with remainder (`QuoRem`), integer exponentiation (`PowInt`), and integer square roots (`SqrtInt`).
- **Advanced Mathematics**: Arbitrary-precision square root (`Sqrt`), real powers (`Pow`), `Hypot`, `Factorial`, `GCD`, `LCM`, and magnitude operations.
- **Transcendental Functions**: Natural logarithm (`Ln`), arbitrary base logarithm (`Log`), `Log10`, `Log2`, `Exp`, `Expm1`, and `Log1p`.
- **Trigonometric & Hyperbolic Suite**: Sine, Cosine, Tangent, Arcsine, Arccosine, Arctangent, degrees/radians conversions, and Hyperbolic variants (`SinH`, `CosH`, `TanH`).
- **Mathematical Constants**: High-precision dynamically calculated `Pi`, `E`, `Phi` (Golden Ratio), `Tau`, plus a fast 1000-digit precomputed `PiCached`.
- **Full Comparison Utilities**: `Cmp`, `Equal`, `NotEqual`, `LT`, `LTE`, `GT`, `GTE`, `Max`, `Min`, `Between`, `Clamp`, `IsInteger`, `IsEven`, and `IsOdd`.
- **Flexible Formatting**: Support for `Fixed` decimal places, `Currency`, `Percent`, `Scientific`, `Engineering`, thousands separators, and human-readable byte sizes (`Bytes`).
- **Lossless Serialization**: Implements `json.Marshaler` and `json.Unmarshaler` (serializes to strings to prevent JSON numeric truncation), as well as big-endian byte slice conversions (`FromBytes`, `ToBytes`).

---

## Why gonum?

Standard Go floating-point types (`float32`, `float64`) are binary representations adhering to IEEE-754. They cannot accurately represent most decimal fractions:

```go
fmt.Println(0.1 + 0.2) // Prints: 0.30000000000000004
```

In `gonum`, numbers are represented as exact decimal digit strings paired with an explicit scale and sign:

```go
a := gonum.New("0.1")
b := gonum.New("0.2")
sum := a.Add(b)
fmt.Println(sum) // Prints: 0.3
```

---

## Installation

```bash
go get github.com/orislabsdev/gonum
```

Requirements: Go `1.22` or later.

---

## Quick Start

### Basic Arithmetic

```go
package main

import (
	"fmt"
	"github.com/orislabsdev/gonum"
)

func main() {
	// Parse from strings (exact)
	a := gonum.New("123.4567890123456789")
	b := gonum.New("987.6543210987654321")

	// Addition and Subtraction
	sum := a.Add(b)
	diff := b.Sub(a)

	// Multiplication
	prod := a.Mul(b)

	// Division with explicit decimal precision (e.g., 10 places)
	quot, err := a.Div(b, 10)
	if err != nil {
		panic(err)
	}

	fmt.Println("Sum: ", sum)
	fmt.Println("Diff:", diff)
	fmt.Println("Prod:", prod)
	fmt.Println("Quot:", quot)
}
```

### Financial Calculations & Rounding

```go
package main

import (
	"fmt"
	"github.com/orislabsdev/gonum"
)

func main() {
	subtotal := gonum.New("1250.755")
	taxRate := gonum.New("0.0825") // 8.25%

	tax := subtotal.Mul(taxRate).Round(2)
	total := subtotal.Add(tax).Round(2)

	// Currency format: prefix, commas, fixed decimals
	fmt.Println("Tax:  ", tax.Currency("$", 2))   // Tax:   $103.19
	fmt.Println("Total:", total.Currency("$", 2)) // Total: $1,353.95

	// Percentage format
	rate := gonum.New("0.1575")
	fmt.Println("Rate: ", rate.Percent(2))        // Rate:  15.75%
}
```

### Scientific Functions & Constants

```go
package main

import (
	"fmt"
	"github.com/orislabsdev/gonum"
)

func main() {
	// High-precision square root
	val := gonum.New("2").SetPrecision(30)
	sqrt2, _ := val.Sqrt()
	fmt.Println("√2 (30 decimals):", sqrt2)

	// Natural logarithm and exponential
	e := gonum.E(25)
	lnE, _ := e.Ln()
	fmt.Println("E:   ", e)
	fmt.Println("ln(E):", lnE)

	// Power with fractional exponents: x^(0.5)
	x := gonum.New("16")
	half := gonum.Half()
	res, _ := x.Pow(half)
	fmt.Println("16^0.5:", res) // 4
}
```

### Trigonometric Functions

```go
package main

import (
	"fmt"
	"github.com/orislabsdev/gonum"
)

func main() {
	// Sine of 30 degrees (should be 0.5)
	angle := gonum.New("30").SetPrecision(10)
	sin30 := angle.SinDeg()
	fmt.Println("sin(30°):", sin30) // 0.5

	// Pi constant to 50 decimal places
	pi := gonum.Pi(50)
	fmt.Println("π (50 digits):", pi)
}
```

### JSON Serialization

`gonum.Number` automatically serializes to a JSON string so that consuming JSON decoders (such as JavaScript's `JSON.parse`) do not truncate 64-bit numbers or decimal precision.

```go
type Transaction struct {
	ID     string        `json:"id"`
	Amount *gonum.Number `json:"amount"`
}

tx := Transaction{
	ID:     "tx_1001",
	Amount: gonum.New("98765432109876543210.123456789"),
}

// Marshalling
data, _ := json.Marshal(tx)
// Output: {"id":"tx_1001","amount":"98765432109876543210.123456789"}

// Unmarshalling (supports both JSON strings and JSON numbers)
var restored Transaction
_ = json.Unmarshal(data, &restored)
```

### Binary Conversions

Convert numbers directly to/from big-endian byte slices for compact storage or network transmission:

```go
n := gonum.New("65535")
bytes := n.ToBytes() // []byte{0xff, 0xff}

restored := gonum.FromBytes(bytes)
fmt.Println(restored) // 65535
```

---

## Architecture & Design

`gonum` represents decimal values using the `Number` struct:

```go
type Number struct {
    negative  bool   // sign of the number
    digits    string // significant digits without leading/trailing zeros
    scale     int    // number of digits following the decimal point
    precision int    // working precision for fractional computations
}
```

- **Immutability**: Methods on `*Number` (such as `Add`, `Sub`, `Mul`, `Round`) return new `*Number` instances without mutating existing values, ensuring thread-safe read patterns.
- **Precision Preservation**: Multiplication accumulates scales (`scale_A + scale_B`). Division calculates digits to the desired precision without cumulative float rounding errors.
- **Normalization**: Leading and trailing zeroes in fractional parts are automatically normalized while preserving numeric fidelity.

---

## API Reference

### Constructors

| Function / Constructor | Description |
| :--- | :--- |
| `New(s string) *Number` | Creates a Number from a string representation; returns `nil` on invalid format. |
| `FromString(s string) *Number` | Alias for `New`. |
| `FromInt64(n int64) *Number` | Creates a Number from an `int64`. |
| `FromUint64(n uint64) *Number` | Creates a Number from a `uint64`. |
| `FromInt(n int) *Number` | Creates a Number from an `int`. |
| `FromFloat64(f float64, prec int) *Number` | Creates a Number from a `float64` with fixed precision. |
| `FromBytes(b []byte) *Number` | Decodes a Number from a big-endian byte slice. |
| `Zero()`, `One()`, `Two()`, `Four()`, `Ten()`, `Half()` | Fast convenience constructors for commonly used constants. |

### Arithmetic Operations

| Method | Description |
| :--- | :--- |
| `Add(other *Number) *Number` | Computes `n + other`. |
| `Sub(other *Number) *Number` | Computes `n - other`. |
| `Mul(other *Number) *Number` | Computes `n * other`. |
| `Div(other *Number, prec ...int) (*Number, error)` | Computes `n / other` with optional decimal precision. Returns error on division by zero. |
| `MustDiv(other *Number, prec ...int) *Number` | Like `Div`, but panics on division by zero. |
| `Mod(other *Number) (*Number, error)` | Computes integer modulo `n % other`. |
| `MustMod(other *Number) *Number` | Like `Mod`, but panics on modulo by zero. |
| `QuoRem(other *Number) (quo, rem *Number, err error)` | Returns integer quotient and remainder simultaneously. |
| `PowInt(exp int) *Number` | Computes `n^exp` for integer exponents. |
| `SqrtInt() *Number` | Computes the integer square root (`largest integer <= sqrt(n)`). |

### Comparison & Predicates

| Method | Description |
| :--- | :--- |
| `Cmp(other *Number) int` | Returns `-1` if `n < other`, `0` if `n == other`, `1` if `n > other`. |
| `Equal(other *Number) bool` | Returns `true` if `n == other`. |
| `NotEqual(other *Number) bool` | Returns `true` if `n != other`. |
| `LT(other *Number) bool` | Returns `true` if `n < other`. |
| `LTE(other *Number) bool` | Returns `true` if `n <= other`. |
| `GT(other *Number) bool` | Returns `true` if `n > other`. |
| `GTE(other *Number) bool` | Returns `true` if `n >= other`. |
| `Max(other *Number) *Number` | Returns the greater of `n` and `other`. |
| `Min(other *Number) *Number` | Returns the lesser of `n` and `other`. |
| `Between(min, max *Number) bool` | Returns `true` if `min <= n <= max`. |
| `Clamp(min, max *Number) *Number` | Constrains `n` within `[min, max]`. |
| `IsZero()`, `IsNegative()`, `IsPositive()` | Sign and zero inspection. |
| `IsInteger()`, `IsEven()`, `IsOdd()` | Parity and integer checks. |

### Mathematical & Rounding

| Method / Function | Description |
| :--- | :--- |
| `Sqrt() (*Number, error)` | Arbitrary-precision square root via Newton's method. |
| `MustSqrt() *Number` | Like `Sqrt`, but panics on negative input. |
| `Pow(exp *Number) (*Number, error)` | Computes `n^exp` supporting fractional exponents. |
| `MustPow(exp *Number) *Number` | Like `Pow`, but panics on error. |
| `Abs() *Number` | Returns the absolute value `\|n\|`. |
| `Neg() *Number` | Returns `-n`. |
| `Round(places int) *Number` | Rounds half-up to `places` decimal positions. |
| `Floor() *Number` | Largest integer less than or equal to `n`. |
| `Ceil() *Number` | Smallest integer greater than or equal to `n`. |
| `Trunc() *Number` | Truncates decimal part towards zero. |
| `Frac() *Number` | Extracts the fractional part. |
| `Hypot(a, b *Number) *Number` | Returns `sqrt(a² + b²)`. |
| `Signum() *Number` | Returns `-1`, `0`, or `1` as a `*Number`. |
| `Factorial(n *Number) (*Number, error)` | Computes `n!` for non-negative integers. |
| `GCD(a, b *Number) *Number` | Greatest common divisor. |
| `LCM(a, b *Number) *Number` | Least common multiple. |

### Logarithms & Exponents

| Method | Description |
| :--- | :--- |
| `Ln() (*Number, error)` | Natural logarithm $\ln(n)$. |
| `Log(base *Number) (*Number, error)` | Arbitrary base logarithm $\log_{\text{base}}(n)$. |
| `Log10() (*Number, error)` | Base-10 logarithm $\log_{10}(n)$. |
| `Log2() (*Number, error)` | Base-2 logarithm $\log_{2}(n)$. |
| `Exp() *Number` | Exponential $e^n$. |
| `Expm1() *Number` | Computes $e^n - 1$ accurately for small numbers. |
| `Log1p() (*Number, error)` | Computes $\ln(1 + n)$ accurately for small numbers. |

### Trigonometric & Hyperbolic

| Method | Description |
| :--- | :--- |
| `Sin() *Number` | Sine (radians). |
| `Cos() *Number` | Cosine (radians). |
| `Tan() *Number` | Tangent (radians). |
| `ASin() (*Number, error)` | Arcsine in $[-1, 1]$ (radians). |
| `ACos() (*Number, error)` | Arccosine in $[-1, 1]$ (radians). |
| `ATan() *Number` | Arctangent (radians). |
| `SinDeg()`, `CosDeg()`, `TanDeg()` | Trigonometric functions accepting degrees. |
| `DegToRad()`, `RadToDeg()` | Degree / radian angle conversions. |
| `SinH()`, `CosH()`, `TanH()` | Hyperbolic sine, cosine, and tangent. |

### Mathematical Constants

| Function | Description |
| :--- | :--- |
| `Pi(precision int) *Number` | $\pi$ computed using Machin's formula to the requested precision. |
| `PiCached() *Number` | Precomputed 1000-digit $\pi$ constant for high-performance usage. |
| `E(precision int) *Number` | Euler's number $e$ to the requested precision. |
| `Phi(precision int) *Number` | Golden ratio $\phi = (1 + \sqrt{5}) / 2$. |
| `Tau(precision int) *Number` | $\tau = 2\pi$ to the requested precision. |

### Formatting & Stringification

| Method | Description |
| :--- | :--- |
| `String() string` | Standard decimal string representation. |
| `Fixed(places int) string` | Formats with exactly `places` decimals. |
| `Currency(symbol string, places int) string` | Formats as currency with thousands separator (e.g. `$1,234.56`). |
| `Percent(places int) string` | Multiplies by 100 and appends `%` (e.g. `12.50%`). |
| `Scientific() string` | Formats in scientific notation (e.g. `1.2345e+4`). |
| `Engineering() string` | Formats in engineering notation (exponents multiple of 3). |
| `Bytes() string` | Human-readable byte representation (`B`, `KB`, `MB`, `GB`, `TB`, `PB`, `EB`). |
| `Format(opt FormatOption) string` | Advanced customization (thousands separator, prefix, suffix, padding, alignment). |
| `Int64() (int64, error)` | Converts to `int64` (truncating decimals); returns error if out of range. |
| `Float64() (float64, error)` | Converts to `float64` (subject to IEEE-754 precision limits). |
| `ToBytes() []byte` | Encodes integer magnitude into big-endian byte slice. |

---

## Running Tests

Run the comprehensive unit test suite:

```bash
go test -v ./...
```

Run tests with race detection and benchmarks:

```bash
go test -v -race ./...
go test -bench=. ./...
```

---

## Contributing

Contributions are warmly welcome! Please read our [CONTRIBUTING.md](CONTRIBUTING.md) guide for details on development workflows, coding standards, and how to submit pull requests.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

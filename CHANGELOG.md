# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2026-10-20

### Added

#### Core Decimal Engine
- Arbitrary-precision decimal arithmetic engine implemented in pure Go with zero external dependencies.
- `Number` struct representing values with exact digit representation, dynamic scale tracking, explicit sign, and configurable precision.
- Constructors:
  - `New(string)` / `FromString(string)` for exact string-based numeric construction.
  - `FromInt64(int64)`, `FromUint64(uint64)`, and `FromInt(int)` for integer construction.
  - `FromFloat64(float64, int)` for bounded float conversion with specified precision.
  - `FromBytes([]byte)` and `ToBytes() []byte` for big-endian binary byte slice encoding and decoding.
  - Predefined constant constructors: `Zero()`, `One()`, `Two()`, `Four()`, `Ten()`, and `Half()`.
- Global precision setting `DefaultPrecision` and instance-level `SetPrecision(int)` and `GetPrecision() int`.

#### Arithmetic Operations
- Fundamental operations: `Add`, `Sub`, and `Mul`.
- Exact division `Div` with configurable decimal precision and error checking against zero division; `MustDiv` panic-on-error variant.
- Integer modulo `Mod` with `MustMod` variant.
- Simultaneous quotient and remainder computation via `QuoRem`.
- Integer exponentiation `PowInt` using binary exponentiation algorithm.
- Integer square root `SqrtInt` using Newton-Raphson iteration.

#### Comparison & Analysis
- Three-way comparison `Cmp` returning `-1`, `0`, or `1`.
- Predicate methods: `Equal`, `NotEqual`, `LT`, `LTE`, `GT`, and `GTE`.
- Extremum utilities: `Max` and `Min`.
- Range inspection: `Between(min, max)` and bounded clamping via `Clamp(min, max)`.
- Numeric property checks: `IsZero`, `IsNegative`, `IsPositive`, `Sign`, `IsInteger`, `IsEven`, and `IsOdd`.

#### Advanced Mathematical Functions
- Arbitrary-precision square root `Sqrt` and `MustSqrt` using Newton's method.
- Real power exponentiation `Pow` and `MustPow` supporting both integer and fractional exponents via logarithm/exponential identities.
- Absolute value `Abs`, negation `Neg`, and cloning `Clone`.
- Rounding utilities:
  - Half-up rounding via `Round(places)`.
  - Floor rounding via `Floor()`.
  - Ceiling rounding via `Ceil()`.
  - Truncation toward zero via `Trunc()`.
  - Fractional part extraction via `Frac()`.
- Geometric hypotenuse `Hypot(a, b)`.
- Signum function `Signum()`.
- Combinatorial and number-theoretic functions: `Factorial`, `GCD` (Greatest Common Divisor), and `LCM` (Least Common Multiple).

#### Transcendental, Logarithmic & Trigonometric Functions
- Natural logarithm `Ln()` with argument reduction and accelerated series expansion.
- Arbitrary base logarithm `Log(base)`.
- Specialized base logarithms `Log10()` and `Log2()`.
- High-precision exponential `Exp()`, with high-accuracy variants `Expm1()` and `Log1p()`.
- Trigonometric functions in radians: `Sin`, `Cos`, `Tan`.
- Inverse trigonometric functions in radians: `ASin`, `ACos`, `ATan`.
- Trigonometric functions with degree inputs: `SinDeg`, `CosDeg`, `TanDeg`.
- Angle unit conversions: `DegToRad` and `RadToDeg`.
- Hyperbolic functions: `SinH`, `CosH`, `TanH`.

#### Precision Mathematical Constants
- `Pi(precision)` dynamically calculated using Machin's arctangent formula.
- `PiCached()` providing precomputed 1000-digit $\pi$ constant for fast access.
- `E(precision)` Euler's number.
- `Phi(precision)` Golden ratio $(1 + \sqrt{5}) / 2$.
- `Tau(precision)` $\tau = 2\pi$.

#### Output & Formatting
- String conversions: `String()`, `GoString()`, `Int64()`, and `Float64()`.
- Fixed-point decimal formatting: `Fixed(places)`.
- Business formatting: `Currency(symbol, places)` and `Percent(places)`.
- Scientific & engineering notations: `Scientific()` and `Engineering()`.
- Human-readable byte size representations: `Bytes()`.
- Highly configurable string formatter `Format(FormatOption)` supporting thousands separators, padding, alignments, prefixes, and suffixes.

#### Serialization & Codecs
- Lossless JSON serialization implementing `encoding/json.Marshaler` and `encoding/json.Unmarshaler`.
- JSON unmarshaling supports both JSON string and JSON numeric representations.

#### Testing & Quality
- Comprehensive unit test suite covering string conversion edge cases, rounding directions, floating-point comparisons, arithmetic precision, trigonometric limits, and JSON serialization.

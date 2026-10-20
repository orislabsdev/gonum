# Contributing to gonum

First off, thank you for considering contributing to `gonum`! 🎉 It is contributors like you who make open source projects thrive.

Please take a few moments to review this document to make the contribution process clear and effective for everyone involved.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
  - [Reporting Bugs](#reporting-bugs)
  - [Suggesting Enhancements](#suggesting-enhancements)
  - [Submitting Pull Requests](#submitting-pull-requests)
- [Development Setup](#development-setup)
  - [Prerequisites](#prerequisites)
  - [Cloning and Testing](#cloning-and-testing)
- [Coding Standards & Architectural Principles](#coding-standards--architectural-principles)
  - [Zero External Dependencies](#zero-external-dependencies)
  - [Immutability and Thread Safety](#immutability-and-thread-safety)
  - [Precision and Mathematical Rigor](#precision-and-mathematical-rigor)
  - [Formatting and Linting](#formatting-and-linting)
  - [Documentation](#documentation)
  - [Testing Standards](#testing-standards)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Pull Request Checklist](#pull-request-checklist)
- [License Notice](#license-notice)

---

## Code of Conduct

We are committed to providing a welcoming, inclusive, and harassment-free environment for all participants. Please treat everyone with respect, kindness, and empathy. Constructive feedback and civil collaboration are expected at all times.

---

## How Can I Contribute?

### Reporting Bugs

Before creating a bug report, please check existing issues to ensure the bug has not already been reported.

When opening an issue, please include:
- A clear and descriptive title.
- Steps to reproduce the issue.
- A minimal, reproducible Go code snippet.
- The expected behavior vs. actual behavior.
- Your Go version (`go version`) and operating system.

### Suggesting Enhancements

Feature requests and performance optimization suggestions are very welcome!
- Check existing issues to see if the proposal has already been discussed.
- Clearly explain the problem the feature solves or the use case it addresses.
- Provide example API signatures or code snippets demonstrating how the new feature would be used.

### Submitting Pull Requests

1. **Fork the repository** on GitHub.
2. **Create a descriptive feature branch** off `main`:
   ```bash
   git checkout -b feat/your-feature-name
   # or
   git checkout -b fix/issue-description
   ```
3. **Implement your changes**, ensuring you write corresponding unit tests.
4. **Run the test suite** and formatters locally.
5. **Commit your changes** following the [Commit Message Guidelines](#commit-message-guidelines).
6. **Push to your fork** and submit a Pull Request against the `main` branch.

---

## Development Setup

### Prerequisites

- **Go**: Version `1.22` or later (refer to `go.mod`).
- **Git**

### Cloning and Testing

Clone your fork locally:

```bash
git clone https://github.com/<your-username>/gonum.git
cd gonum
```

Run the unit test suite:

```bash
go test -v ./...
```

Run tests with race detection:

```bash
go test -v -race ./...
```

Run benchmarks:

```bash
go test -bench=. -benchmem ./...
```

Verify formatting and vetting:

```bash
go vet ./...
gofmt -s -l .
```

---

## Coding Standards & Architectural Principles

When writing code for `gonum`, please keep the following core principles in mind:

### 1. Zero External Dependencies
`gonum` is intentionally designed to be completely self-contained. **No external third-party packages may be introduced.** All algorithms and utilities must rely exclusively on the Go standard library.

### 2. Immutability and Thread Safety
All arithmetic operations (such as `Add`, `Sub`, `Mul`, `Div`, `Round`) must treat `*Number` as an immutable value. Methods should return a newly allocated `*Number` without mutating the receiver's state, enabling safe concurrent reading.

### 3. Precision and Mathematical Rigor
- Exact decimal computations must avoid intermediate conversions to IEEE-754 floating-point values whenever possible.
- If an algorithm uses Taylor series or iterative approximations (e.g., Newton's method in `Sqrt` or series expansions in trigonometric and logarithmic functions), ensure sufficient working precision is maintained so that rounding to the target precision produces correct results.

### 4. Formatting and Linting
- All code must be formatted using standard `gofmt` (`gofmt -s -w .`).
- Variable and function names should follow idiomatic Go conventions (camelCase, PascalCase for exported symbols, short and concise variable names where appropriate).

### 5. Documentation
- All exported functions, methods, types, and package constants must have clear, informative Go docstrings.
- Docstrings should explain parameters, precision implications, and any error conditions (such as division by zero).

### 6. Testing Standards
- Every new feature, bug fix, or edge case must include comprehensive unit tests in `*_test.go`.
- Test extreme cases: zero values, negative numbers, very large values, numbers with large fractional precision, and invalid inputs.

---

## Commit Message Guidelines

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```text
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Types:
- `feat`: A new feature or API capability.
- `fix`: A bug fix.
- `docs`: Documentation changes (e.g., README, godoc comments).
- `test`: Adding or refactoring tests.
- `perf`: Performance improvements.
- `refactor`: Code changes that neither fix a bug nor add a feature.
- `chore`: Maintenance tasks, repo configurations, or CI updates.

### Examples:
- `feat(arithmetic): implement SqrtInt using Newton's method`
- `fix(format): prevent out-of-bounds error on negative single-digit currency`
- `docs(readme): add examples for JSON unmarshaling`
- `perf(log): optimize atanh series working precision`

---

## Pull Request Checklist

Before opening or requesting a review on your PR, please verify:

- [ ] All tests pass cleanly (`go test -v -race ./...`).
- [ ] Code is formatted with `gofmt -s -w .`.
- [ ] Static analysis passes (`go vet ./...`).
- [ ] New or modified code is covered by tests.
- [ ] Public API methods and types are fully documented with Go docstrings.
- [ ] The `CHANGELOG.md` has been updated under `[Unreleased]` if your change introduces new features, breaking changes, or bug fixes.
- [ ] Commit history is clean, readable, and adheres to Conventional Commits.

---

## License Notice

By contributing to `gonum`, you agree that your contributions will be licensed under the project's [MIT License](LICENSE).

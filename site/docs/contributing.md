# Contributing

We welcome contributions to Canton DeFi Primitives.

---

## How to Contribute

### 1. Report Issues

Open an issue on [GitHub](https://github.com/GyberExperiments/canton-primitives/issues) with:

- Clear description of the bug or feature request
- Steps to reproduce (for bugs)
- Expected vs actual behavior
- DAML SDK version

### 2. Submit Pull Requests

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes
4. Run tests: `cd daml && daml test`
5. Submit a PR with clear description

### 3. Review Contracts

Academic and security review of the DAML contracts is especially valued. Focus areas:

- Invariant completeness — are all mathematical properties enforced?
- Authorization model — can any party perform unauthorized actions?
- Status transitions — are all state machine transitions valid?
- Edge cases — partial fills, zero amounts, expiry boundaries

## Code Style

### DAML Conventions

- Module-level documentation: `-- | Module description`
- Choice-level documentation: `-- | Choice description`
- Inline fix documentation: `-- FIX P0-XX: Description`
- Use `assertMsg` with descriptive messages
- Group fields with section comments: `-- === Section ===`

### Naming

- Templates: PascalCase (`OtcOffer`, `Settlement`)
- Choices: PascalCase (`Accept`, `PartialRelease`)
- Fields: camelCase (`offerId`, `currentAmount`)
- Types: PascalCase (`OtcStatus`, `EscrowStatus`)

## Testing

All changes must include or update tests:

```bash
cd daml
daml test
```

Test patterns:

- **Lifecycle tests** — full workflow from creation to completion
- **Edge case tests** — boundary conditions, minimum/maximum values
- **Negative tests** — `submitMultiMustFail` for unauthorized actions
- **Invariant tests** — verify mathematical properties hold

## Areas We Need Help

- **DAML expertise** — contract optimization and new primitives
- **Rust SDK** — gRPC client implementation
- **Security review** — formal verification of contracts
- **Documentation** — tutorials, guides, translations
- **Testing** — additional test scenarios and stress testing

## License

By contributing, you agree that your contributions will be licensed under GPL v3.

## Contact

- GitHub: [GyberExperiments](https://github.com/GyberExperiments)
- Organization: [TheMacroeconomicDao](https://github.com/TheMacroeconomicDao)

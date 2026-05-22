# Asset Tokenization

A DAML project that models issuance and secondary transfer of tokenized Treasury Bills (T-Bills). The workflow includes issuer-controlled issuance, investor compliance checks, custody metadata, and an escrow-based secondary sale.

## Project layout

```text
.
├── daml.yaml                 # Production contract package
├── multi-package.yaml        # Multi-package build configuration
├── daml/
│   ├── Main.daml
│   ├── Types.daml
│   └── Contracts/
│       ├── Compliance.daml
│       ├── Escrow.daml
│       ├── TBill.daml
│       └── TBillIssuance.daml
└── tests/
    ├── daml.yaml             # DAML Script test package
    └── daml/Tests/TBillTest.daml
```

The contract package and the test package are intentionally separate.

## Requirements

- DPM / DAML SDK `3.4.11`

Check the installed version:

```sh
dpm version
```

## Build

Build all packages from the repository root:

```sh
dpm build --all
```

This builds:

- `.daml/dist/Asset-Tokenization-DAML-1.0.0.dar`
- `tests/.daml/dist/Asset-Tokenization-DAML-tests-1.0.0.dar`

## Test

Run the DAML Script tests from the repository root:

```sh
dpm test --package-root tests
```

If you are running on JDK 25 and see `sun.misc.Unsafe` runtime warnings from the DAML script service, they are emitted by the JVM/runtime dependency rather than by this project. You can suppress that JDK warning with:

```sh
JAVA_TOOL_OPTIONS='--sun-misc-unsafe-memory-access=allow' dpm test --package-root tests
```

## Test scenario

`Tests.TBillTest:testTBillWorkflow` verifies the end-to-end flow:

1. allocate `Issuer`, `Regulator`, `Custodian`, `Investor1`, and `Investor2` parties;
2. create compliance records for both investors;
3. create compliance-check contracts for both investors;
4. issue a T-Bill to `Investor1`;
5. create an escrow contract for a secondary sale from `Investor1` to `Investor2`;
6. accept the escrow as both seller and buyer;
7. transfer the T-Bill to `Investor2` after compliance verification.

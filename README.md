# Gnark Plonky2 Verifier

This is an implementation of a [Plonky2](https://github.com/mir-protocol/plonky2) verifier in Gnark (supports Groth16 and PLONK).

Besides the verifier, there are some Gnark implementation of circuits in this repo that may be useful for other projects:

- [Goldilocks](https://github.com/succinctlabs/gnark-plonky2-verifier/blob/main/field/field.go)
- [Poseidon](https://github.com/succinctlabs/gnark-plonky2-verifier/blob/main/poseidon/poseidon.go)
- [FRI](https://github.com/succinctlabs/gnark-plonky2-verifier/blob/main/plonky2_verifier/fri.go)

## Chainreels maintenance

Chainreels uses this fork as the pinned reference verifier for ZKArch's
Plonky2-to-Gnark wrap interoperability check. The production pin is:

```text
853a273aa9984cad8b693b4edd637940c351a2b5
```

That revision carries two compatibility patches on top of the
`succinctlabs/gnark-plonky2-verifier` history:

1. `1d96750760deaa4d8f307cd5ddcced9ac6cf1e04` updates the Goldilocks
   multiplicative-group and power-of-two generators to match
   [plonky2#1579].
2. `853a273aa9984cad8b693b4edd637940c351a2b5` observes the serialized FRI
   configuration and parameters before deriving transcript challenges, matching
   [plonky2#1678]. It also restores the constant-arity reduction strategy value
   omitted by Plonky2's `CommonCircuitData` JSON serializer.

Keep these commits in this order when rebasing onto a newer upstream revision.
At the pinned revision, the inherited full test suite is not a clean release
gate: fixtures generated before the two compatibility changes reject under the
new transcript, and older range-check tests hit the repository's existing
`nbBits should be 16` limitation. These are baseline failures, not changes made
by the Chainreels fork.

Before moving ZKArch's pin, run the unaffected package tests
(`go test ./types ./variables`) and ZKArch's real `wrap-interop` job. The pin
must not move unless the exported ZKArch proof verifies against the same commit.

[plonky2#1579]: https://github.com/0xPolygonZero/plonky2/pull/1579
[plonky2#1678]: https://github.com/0xPolygonZero/plonky2/pull/1678

## Requirements

- [Go (1.19+)](https://go.dev/doc/install)

## Benchmark

To run the benchmark,
```
go run benchmark.go
```

## Profiling

First run the benchmark with profiling turned on
```
go run benchmark.go -profile
```

Then use the following command to generate a visualization of the pprof
```
go tool pprof --png gnark.pprof > verifier.png
```
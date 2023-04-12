---
title: New opcode: fixed point exp
description: Exponentianion with fixed point inputs and outputs
author: Renan Rodrigues de Souza (@RenanSouza2)
discussions-to: 
status: Draft
type: Standards Track
category: Core
created: 12/04/2023
---

## Abstract

This EIP proposes a new opcode, FIXEXP that consumes two stack elements and computes the exponent interpreting the inputs as fixed point numbers with 18 decimal places

## Motivation

With the rise of DeFi many contracts compute APY and thus implement in one way or another a numerical aproximation of the fixed point exponentiation.

Those approximations present a strong trade-off between gas use and numerical precision.

The objective of this opcode is to eliminate many hard design decisions and error-prone custom implementations.

## Specification


### stack

| Constant         | Value                  |
|:-----------------|:-----------------------|
| `FORK_BLOCK_NUM` | TBD                    |
| `DECIMALS`       | `18`                   |
| `UNIT`           | `1000000000000000000`  |

Starting from `BLOCK_NUM >= FORK_BLOCK_NUM` a new istruction is incruded:

`FIXEXP` : `0x0C`

The `FIXEXP` instruction (fixed point exponentiation) pops 2 values from the stack, first `base` and then `exponent`, and pushes on the stack `res`. `base`, `exponent` and `aux` are interpreted as fixed point numbers with 18 decimal places.

```
if(base != 0)
aux = floor18(base ^ exponent)
res = uint(aux) mod 2^256

if(base == 0 && exponent != 0)
res = 0

if(base == 0 && exponent == 0)
res = UNIT
```

`floor18(a, b)` is defined ads `a` rounded down with 18 decimal places. For this `base ^ exponent` must be calculated with at least 19 decimal places.

The `uint()` conversion is just a notation to represent the different number interpretation between steps.

### gas

NOT DONE YET

## Rationale

The order of arguments and the 0^0 treatment the same of the exising opcode `EXP`.

This is the first EIP that is explictly treats fixed point decimal numbers, as such, there is no precedent on how to define them. For the sake of simplicity the number o decimal places is fixed to be 18.

The arguments are unsigned because non integer exponentiation is not w
It would alse be useful to use negative numbers in the `exponent`. It is not simple to  but this case is better dealt by using the property:

```
a^(-b) = 1/(a^b)
```

This EIP does not require [EIP-5000](./eip-5000.md) but would bennefit from it

## Backwards Compatibility

This EIP introduces a new OPCODe therefore needs to be included in a hard fork.

## Test Cases

1. ```
   PUSH8 0x1BC16D674EC80000
   PUSH8 0x1BC16D674EC80000
   FIXEXP
   ---
   0x3782DACE9D900000
   ```

## Reference Implementation

<!--
  This section is optional.

  The Reference Implementation section should include a minimal implementation that assists in understanding or implementing this specification. It should not include project build files. The reference implementation is not a replacement for the Specification section, and the proposal should still be understandable without it.
  If the reference implementation is too large to reasonably be included inline, then consider adding it as one or more files in `../assets/eip-####/`. External links will not be allowed.

  TODO: Remove this comment before submitting
-->

## Security Considerations

<!--
  All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. For example, include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.

  The current placeholder is acceptable for a draft.

  TODO: Remove this comment before submitting
-->

Needs discussion.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

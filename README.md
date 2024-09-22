# solidity-testing

## Types of Tests

- unit tests
- integrations test --> test the scripts
- forked tests --> run unit tests on a fork of mainnet or testnet
- staging tests --> run tests directly on mainnet or testnet

- fuzzing
- stateful fuzz
- stateless fuzz
- formal verification

## Fuzz/Invariant Testing

Fuzz/Invariant Testing: when we supply random data to the system in an attempt to break it.

Invariant: Property of the system that should always hold

Example: 

```
function testIsAlwaysGetZeroFuzz(uint256 data) public {
  exampleContract.doStuff(data);
  assert(exampleContract.shouldAlwaysBeZero() == 0);
}
```

### Flow to Write Fuzz Tests

1) Understand the Invariants (in above example shouldAlwaysBeZero needs to equal zero at all times)
2) Write a fuzz test for invariant (let it run through multiple possibilities to make sure shouldAlwaysBeZero == 0 at all times)

### Stateless Fuzzing 

The above example is an example of a stateless fuzz test, where the state of the previous run is discarded for every new run.

### Stateful Fuzzing (Invariant Testing)

Fuzzing where the final state of the previous run is the starting state of the next run

#### Write a Stateful Fuzz Test in Foundry

import StdInvariant, inherit it in the Test Contract and needs to be inialized in the setUp function. Then we can write our statefull fuzz function:

```
import {StdInvariant} from "forge-std/StdInvariant.sol"
contract ContractTest is StdInvariant, Test {
  function setUp() {
    exampleContract = new ExampleContract();
    targetContract(address(exampleContract));
}

  function invariant_testAlwaysReturnsZero() public {
  assert(exampleContract.shouldAlwaysBeZero() == 0);
}
}
```

Foundry would then call our functions in a random order and using random data.

P.S: In foundry invariants usually means stateful fuzzing, and fuzzing means stateless fuzzing

#### Handler-Based Testing

Setup an Invariants contract where we define our invariants. In the setup function we will target a handler contract.

The handler contract will call functions of our contract in specific ways and make sure that the environment is set to properly fuzz test the contract.

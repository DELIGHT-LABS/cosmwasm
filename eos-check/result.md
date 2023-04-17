## Overview
This report investigates the cross-compatibility of WASM files generated from Antelope CDT and Cosmwasm. To do so, we run the Cosmwasm validation tool (cosmwasm-check) and the Antelope CDT validation tool (wasm-validate). We verify that the constraints on both virtual machines are acceptable for Antelope's Cosmwasm integration in terms of the WASM specification.

## Cosmwasm to Antelope CDT
wasm-validate is a tool in Antelope CDT that reads WASM files and checks their validity. We used this tool to analyze Cosmwasm-generated WASM files below (in case of source code, built without optimization) , and all of them passed without any failure. These contracts range from the basic counter contract to the complicated contracts that we are currently using for the decentralized exchange on the Terra network.

### Target WASM Files
wasm-validate: 1.0.27 (https://git.launchpad.net/ubuntu/+source/wabt)

- https://github.com/CosmWasm/cw-template.git
- https://github.com/CosmWasm/cosmwasm/tree/main/packages/vm/testdata/cyberpunk.wasm
- https://github.com/CosmWasm/cosmwasm/tree/main/packages/vm/testdata/floaty.wasm
- https://github.com/CosmWasm/cosmwasm/tree/main/packages/vm/testdata/hackatom.wasm
- https://github.com/CosmWasm/cosmwasm/tree/main/packages/vm/testdata/ibc_reflect.wasm
- https://github.com/terraswap/terraswap/contracts/terraswap_factory
- https://github.com/terraswap/terraswap/contracts/terraswap_pair
- https://github.com/terraswap/terraswap/contracts/terraswap_router
- https://github.com/terraswap/terraswap/contracts/terraswap_token

## Antelope CDT to Cosmwasm
cosmwasm-check is a tool in Cosmwasm that checks the validity of a WASM file, as well as its interface and version. Although a few lines of code in package/vm/src/compatibility.rs have been modified, most of them are function names in the Antelope contract interface or disabling the Cosmwasm interface version check. With this tool, we found that all Antelope CDT-generated WASM files pass successfully.

The recently added bound table check limits the use of an unbounded table in a contract. This limitation is commented out in the modified source code. It was added to the cosmwasm main branch after the research proposal was completed, so it was not mentioned in the original report.

### Target WASM Files
cosmwasm-check: https://github.com/DELIGHT-LABS/cosmwasm/tree/main/packages/check

- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/dice.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/eosio.msig.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/eosio.sudo.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/eosio.system.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/eosio.token.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/exchange.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/hello.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/identity.wasm
- https://github.com/AntelopeIO/eos-vm/blob/main/tests/fuzz/afl_in/proxy.wasm

### Compatibility Check Result
```
cosmwasm/target/debug main $ ./cosmwasm-check ./dice.wasm                                                                                                                                            [17:29:56]
Available capabilities: {"stargate", "iterator", "cosmwasm_1_1", "cosmwasm_1_2", "staking"}

./dice.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./eosio.msig.wasm                                                                                                                                      [17:33:51]
Available capabilities: {"cosmwasm_1_2", "staking", "iterator", "stargate", "cosmwasm_1_1"}

./eosio.msig.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./eosio.sudo.wasm                                                                                                                                      [17:34:04]
Available capabilities: {"cosmwasm_1_2", "staking", "iterator", "cosmwasm_1_1", "stargate"}

./eosio.sudo.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./eosio.system.wasm                                                                                                                                    [17:34:11]
Available capabilities: {"cosmwasm_1_1", "stargate", "cosmwasm_1_2", "staking", "iterator"}

./eosio.system.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./eosio.token.wasm                                                                                                                                     [17:34:16]
Available capabilities: {"iterator", "stargate", "cosmwasm_1_2", "staking", "cosmwasm_1_1"}

./eosio.token.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./exchange.wasm                                                                                                                                        [17:34:21]
Available capabilities: {"staking", "cosmwasm_1_1", "iterator", "cosmwasm_1_2", "stargate"}

./exchange.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./hello.wasm                                                                                                                                           [17:34:31]
Available capabilities: {"cosmwasm_1_2", "stargate", "staking", "iterator", "cosmwasm_1_1"}

./hello.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./identity.wasm                                                                                                                                        [17:34:36]
Available capabilities: {"iterator", "stargate", "staking", "cosmwasm_1_1", "cosmwasm_1_2"}

./identity.wasm: pass

All contracts (1) passed checks!

cosmwasm/target/debug main $ ./cosmwasm-check ./proxy.wasm                                                                                                                                           [17:34:41]
Available capabilities: {"stargate", "iterator", "cosmwasm_1_1", "cosmwasm_1_2", "staking"}

./proxy.wasm: pass

All contracts (1) passed checks!
```

## Conclusion
While interface mapping between Cosmwasm and Antelope CDT is necessary, the aforementioned validations show that the constraints for wasm specification are almost identical and cross-compatible in both, as cited in the original report.

Executing WASM file on both VMs has a few blockers such as interface mapping, parameter deserialization, and argument allocation. These can be addressed after building the integration, which requires more work than the research. We will respond to the comments on our proposal sincerely and try to help with understanding as much as possible.

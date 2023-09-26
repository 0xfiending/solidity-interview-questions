# Solidity Interview Questions

Over 140 interview questions for Ethereum Developers
All of these questions can be answered in three sentences or less. </br></br>

The answers provided are aggregated from my personal research and knowledge. </br>
Solutions that are not confident or are incomplete will be preceded with: * </br>

# Easy
1. What is the difference between private, internal, public, and external functions?
- Private functions can only be called by other functions within the immediate contract.
- Internal functions can only be called by other functions within the immediate contract and contracts that inherit the immediate contract.
- Public functions can be called by anyone - immediate contract, outsides contracts, EOAs, etc.
- External functions can only be called by outside contracts and EOAs.

2. Approximately, how large can a smart contract be?
- EIP170 introduces code size limit of 24 kilobytes.
- Contract creation will fail with a gas error if larger.

3. What is the difference between create and create2?
- CREATE uses the sending account and the sending account's current nonce.
  - keccak256(abi.encodePacked(msg.sender, nonce))  
- CREATE2 opcode allows deterministic creation of contract addresses.
  - CREATE2 uses the sending account, the contract initcode, and a custom salt.
  - keccak256(0xff + msg.sender + salt + keccak256(initcode)) 

4. What major change with arithmetic happened with Solidity 0.8.0?
- Overflows and underflows will throw an error.

5. What special CALL is required for proxies to work?
- * delegatecall ?
  
6. Prior to EIP-1559, how do you calculate the dollar cost of an Ethereum transaction?
- *

7. What are the challenges of creating a random number on the blockchain?
- RNG on the blockchain is deterministic. Global variables such as block.number, block.timestamp,
tx.origin, etc. should not be used as a seed to a RNG.
- Instead, it is recommended to use a trusted RNG Oracle, such as Chainlink.

8. What is the difference between a Dutch Auction and an English Auction?
- In an English Auction, an auction takes place over a duration (i.e. 7 days), where the starting price of the item(s) is low and increases as bidders place bids.
  - At the end of the duration, the bidder with the highest bid may claim the item(s).
  - All other bidders may withdraw their bid / are refunded at the end of the duration.

- In a Dutch Auction, an auction takes places over a duration (i.e. 7 days), where the starting price of the item(s) starts high and decreases over time (opposite of English Auction).
  - The auction will end prematurely if a bidder decides to purchase the item at the current auction price and claim the item(s).  

9. What is the difference between transfer and transferFrom in ERC20?
- transfer() will transfer the value supplied to the _to address.
- transferFrom() will transfer the value supplied from the _from address to the _to address.
- transferFrom() is often used with contracts and an approve() call precedes a transferFrom() call.

10. Which is better to use for an address allowlist: a mapping or an array? Why?
- * Mapping, because look ups will be cheaper compared to an unsorted array, which will have to iterated each time the allowlist is checked.

11. Why shouldn’t tx.origin be used for authentication?
- * tx.origin is global variable that can be spoofed by having a contract in the middle. (Redo - better explanation)

12. What hash function does Ethereum primarily use?
- keccak256 / sha3

13. How much is 1 gwei of Ether?
- 1 gwei = 1 * 10^9

14. How much is 1 wei of Ether?
- 1 wei = 1

15. What is the difference between assert and require?
- Assert is traditionally used in test cases to validate a condition is true or false.
- Require is used for validating conditions and propagating errors in source code.

16. What is a flash loan?
- A flash loan is where a user borrows funds, uses funds, then pays back the funds all in a single transaction. There is no risk for the lender.

17. What is the check-effects pattern?
- Checks-Effects-Interactions is a pattern that is often used to mitigate reentrancy risks. The necessary conditions are checked, then the necessary state changes are made, then external calls / transfers are made last.

18. What is the minimum amount of Ether required to run a solo staking node?
- 32 ETH is required to run a full Ethereum node.

19. What is the difference between fallback and receive?
- Either fallback or receive can be called when Ether is sent to a contract.
  - fallback() will be called if msg.data is empty or if receive() is not implemented.
  - fallback() is also called in situations where the function selector used in the call to the contract does not match an existing selector.

20. What is reentrancy?
- Reentrancy is when there is a callback into the calling contract during a transaction's execution.

21. As of the Shanghai upgrade, what is the gas limit per block?
- * 30 million gas is the maximum allowed. The target block size is 15 million gas. The block base fee will fluctuate based on network usage.

22. What prevents infinite loops from running forever?
- Gas limit and Block gas limit.
  - Gas limit is the amount of gas that the sender of the transaction is willing to use for the transaction (transaction-level limit).
  - Block gas limit is a limit set by the network for the total computation allowed in a single block, this is voted and set by validators. (network-level limit)

23. What is the difference between tx.origin and msg.sender?
- tx.origin can never be a contract. Will always be an EOA.
- msg.sender can be spoofed to be a contract.

24. How do you send Ether to a contract that does not have payable functions, or a receive or fallback?
- It is possible to force send ether using selfdestruct.

25. What is the difference between view and pure?
- pure and view are reserved keywords in Solidity that set the visibility of a function.
  - Pure functions promise to not read or change blockchain state.
  - View functions promise to not change the blockchain state. 

26. What is the difference between transferFrom and safeTransferFrom in ERC721?
- safeTransferFrom checks if the receive is able to handle ERC721s.
  - The goal is to ensure that NFTs are not locked or lost.
  - Used with onERC721Received hook 

- transferFrom is the standard way to send NFTs and does not include checks for the receiver.

27. How can an ERC1155 token be made into a non-fungible token?
- *

28. What is access control and why is it important?
- Access Control is gating access to public / external functions to only specific authorized addresses.
- * Importance 

29. What does a modifier do?
- * A function modifier allows checks to occur before a function's execution. This provides a way to re-use checks numerous places in the contract to save gas if checks are used more than once. 

30. What is the largest value a uint256 can store?
- (2 ^ 256) - 1

31. What is variable and fixed interest rate?
- Variable rates will adjust over time in response to market conditions.
- Fixed rates are locked in when the user obtains the loan.

# Medium
1. What is the difference between transfer and send? Why should they not be used?
- transfer() uses 2300 gas and throws an error on failure. transfer() will fail if fallback/receive hook does not exist.
- send() also sends 2300 gas. This function returns a bool value.
- call() returns a bool and memory values. This function triggers the fallback/receive hook of the receiving address. All gas is forwarded.
  - Call is the recommended way to send ether. 

2. How do you write a gas-efficient for loop in Solidity?
- Use ++i, instead of i++ </br>
`for (uint i = 0; i < 100; ) {
    // do stuff
    unchecked { ++i }
}`

3. What is a storage collision in a proxy contract?
- Proxy contracts rely on the delegatecall() method, which uses the originating contract's storage for the call.
- Storage collision occurs when the callee's storage variable layout deviates from the caller's storage variable layout.
  - This could cause the caller's storage to be overwritten. 

4. What is the difference between abi.encode and abi.encodePacked?
- There can be a hash collision with abi.encodePacked()
- Hash collisions cannot occur with abi.encode()

5. uint8, uint32, uint64, uint128, uint256 are all valid uint sizes. Are there others?
- uint is shorthand for uint256

6. What changed with block.timestamp before and after proof of stake?
- * Block intervals are set at 12 seconds. Each slot has an expected timestamp.

7. What is frontrunning?
- A MEV opportunity where a transaction is copied and executed with a higher gas price in order to be included in the block prior to another.

8. What is a commit-reveal scheme and when would you use it?
- A commit-reveal scheme is where a user will hash data off-chain and submit it to a transaction in order to mask the true value from being revealed.
  - The value can be validated on-chain by hashing the necessary inputs and comparing hash values.
- Commit-reveal schemes can be used in protocols where the data being submitted by the user needs to remain private (i.e. a game).
- Commit-reveal schemes can also be used to mitigate front-running in certain cases. 

9. Under what circumstances could abi.encodePacked create a vulnerability?
- This function can cause a vulnerability if it is used for signature verification, where the user supplies values to the encodedPacked() method.
  - This may cause a scenario where a hash collision can occur and a malicious user can sign as another user to the protocol.

10. How does Ethereum determine the BASEFEE in EIP-1559?
- The proposal was implemented as part of the London Hard Fork.
  - The proposal introduces a base fee for all transactions and increases the block size.
  - If the block is more than 50% full, the base fee will be increased by 12.5%.
  - If the block is empty, the base fee will be decreased by 12.5%. 

11. What is the difference between a cold read and a warm read?
- Refers to Storage Access.
  - A cold read is the first time a storage variable is accessed.
  - A warm read is all subsequent accesses to the same storage variable. 

12. How does an AMM price assets?
- *

13. What is a function selector clash in a proxy and how does it happen?
- * Function selector clash with a proxy can occur when the function selector of a proxy function matches the function selector of a callee function.

14. What is the effect on gas of making a function payable?
- Execution cost is decreased by 24.
  - Payable functions include less opcodes, specifically msg.value == zero check is bypassed. 

15. What is a signature replay attack?
- Signature replay attack is when a signature is used more than once to validate for a user.
  - This often occurs with the usage of keccak256 with encodePacked, and ecrecover.
- It is recommended to have the user hash the message off-chain and supply the hashed value as input to a function.
  - Do this instead of directly hashing the values on-chain, where the user can supply the inputs.    
- It is also recommended to use nonces to signify how many times a signature has been used.

16. What is gas griefing?

17. How would you design a game of rock-paper-scissors in a smart contract such that players cannot cheat?

18. What is the free memory pointer and where is it stored?

19. What function modifiers are valid for interfaces?

20. What is the difference between memory and calldata in a function argument?

21. Describe the three types of storage gas costs.

22. Why shouldn’t upgradeable contracts use the constructor?

23. What is the difference between UUPS and the Transparent Upgradeable Proxy pattern?

24. If a contract delegatecalls an empty address or an implementation that was previously self-destructed, what happens? What if it is a regular call instead of a delegatecall?

25. What danger do ERC777 tokens pose?

26. According to the solidity style guide, how should functions be ordered?

27. According to the solidity style guide, how should function modifiers be ordered?

28. What is a bonding curve?

29. How does safeMint differ from mint in the OpenZeppelin ERC721 implementation? 

30. What keywords are provided in Solidity to measure time?

31. What is a sandwich attack?

32. If a delegatecall is made to a function that reverts, what does the delegatecall do?

33. What is a gas efficient alternative to multiplying and dividing by a multiple of two?

34. How large a uint can be packed with an address in one slot?

35. Which operations give a partial refund of gas?

36. What is ERC165 used for?

37. If a proxy makes a delegatecall to A, and A does address(this).balance, whose balance is returned, the proxy's or A?

38. What is a slippage parameter useful for?

39. What does ERC721A do to reduce mint costs? What is the tradeoff?

40. Why doesn't Solidity support floating point arithmetic?

41. What is TWAP?

42. How does Compound Finance calculate utilization?

43. If a delegatecall is made to a function that reads from an immutable variable, what will the value be?

# Hard
1. How does fixed point arithmetic represent numbers?

2. What is an ERC20 approval frontrunning attack?

3. What opcode accomplishes address(this).balance?

4. How many arguments can a solidity event have?

5. What is an anonymous Solidity event?

6. Under what circumstances can a function receive a mapping as an argument?

7. What is an inflation attack in ERC4626

8. How many arguments can a solidity function have?

9. How many storage slots does this use? uint64[] x = [1,2,3,4,5]? Does it differ from memory?

10. Prior to the Shanghai upgrade, under what circumstances is returndatasize() more efficient than push zero?

11. Why does the compiler insert the INVALID op code into Solidity contracts?

12. What is the difference between how a custom error and a require with error string is encoded at the EVM level?

13. What is the kink parameter in the Compound DeFi formula?

14. How can the name of a function affect its gas cost, if at all?

15. What is a common vulnerability with ecrecover?

16. What is the difference between an optimistic rollup and a zk-rollup?

17. How does EIP1967 pick the storage slots, how many are there, and what do they represent?

18. How much is one Sazbo of ether?

19. What can delegatecall be used for besides use in a proxy?

20. Under what circumstances would a smart contract that works on Etheruem not work on Polygon or Optimism? (Assume no dependencies on external contracts)

21. How can a smart contract change its bytecode without changing its address?

22. What is the danger of putting msg.value inside of a loop?

23. Describe the calldata of a function that takes a dynamic length array of uint128 when uint128[1,2,3,4] is passed as an argument

24. Why is strict inequality comparisons more gas efficient than ≤ or ≥? What extra opcode(s) are added?

25. If a proxy calls an implementation, and the implementation self-destructs in the function that gets called, what happens?

26. What is the relationship between variable scope and stack depth?

27. What is an access list transaction?

28. How can you halt an execution with the mload opcode?

29. What is a beacon in the context of proxies?

30. Why is it necessary to take a snapshot of balances before conducting a governance vote?

31. How can a transaction be executed without a user paying for gas?

32. In solidity, without assembly, how do you get the function selector of the calldata?

33. How is an Ethereum address derived?

34. What is the metaproxy standard?

35. If a try catch makes a call to a contract that does not revert, but a revert happens inside the try block, what happens?

36. If a user calls a proxy makes a delegatecall to A, and A makes a regular call to B, from A's perspective, who is msg.sender? from B's perspective, who is msg.sender? From the proxy's perspective, who is msg.sender?

37. Under what circumstances do vanity addresses (leading zero addresses) save gas?

38. Why do a significant number of contract bytecodes begin with 6080604052? What does that bytecode sequence do?

39. How does Uniswap V3 determine the boundaries of liquidity intervals?

40. What is the risk-free rate?

41. When a contract calls another call via call, delegatecall, or staticcall, how is information passed between them?

# Advanced
1. What addresses to the ethereum precompiles live at?

2. How does Solidity manage the function selectors when there are more than 4 functions?

3. If a delegatecall is made to a contract that makes a delegatecall to another contract, who is msg.sender in the proxy, the first contract, and the second contract?

4. How does ABI encoding vary between calldata and memory, if at all?

5. What is the difference between how a uint64 and uint256 are abi-encoded in calldata?

6. What is read-only reentrancy?

7. What are the security considerations of reading a (memory) bytes array from an untrusted smart contract call?

8. If you deploy an empty Solidity contract, what bytecode will be present on the blockchain, if any?

9. How does the EVM price memory usage?

10. What is stored in the metadata section of a smart contract?

11. What is the uncle-block attack from an MEV perspective?

12. How do you conduct a signature malleability attack?

13. Under what circumstances do addresses with leading zeros save gas and why?

14. What is the difference between payable(msg.sender).call{value: value}(””) and msg.sender.call{value: value}(””)?

15. How many storage slots does a string take up?

16. How does the --via-ir functionality in the Solidity compiler work?

17. Are function modifiers called from right to left or left to right, or is it non-deterministic?

18. If you do a delegatecall to a contract and the opcode CODESIZE executes, which contract 2. size will be returned?

19. Why is it important to ECDSA sign a hash rather than an arbitrary bytes32?

20. Describe how symbolic manipulation testing works.

21. What is the most efficient way to copy regions of memory?

22.  How can you validate on-chain that another smart contract emitted an event, without using an oracle?

23. When selfdestruct is called, at what point is the Ether transferred? At what point is the smart contract's bytecode erased?

24. Under what conditions does the Openzeppelin Proxy.sol overwrite the free memory pointer? Why is it safe to do this?

25. Why did Solidity deprecate the "years" keyword?

26. What does the verbatim keyword do, and where can it be used?

27. How much gas can be forwarded in a call to another smart contract?

28. What does an int256 variable that stores -1 look like in hex?

29. What is the use of the signextend opcode?

30. Why do negative numbers in calldata cost more gas?

31. What is a zk-friendly hash function and how does it differ from a non-zk-friendly hash function?

32. What is a nullifier in the context of zero knowledge, and what is it used for?

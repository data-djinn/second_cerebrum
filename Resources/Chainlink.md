==Decentralized Oracle Service which aims to provide off-chain exposure to the smart contracts of a network.==
- uses a decentralized oracle network to allow blockchains to securely interact with external data feeds like:
        - price
        - payments
        - event data
- Utilizes LINK token to pay Chainlink Node operators for their services
- Fixed supply of 1 Billion

## How it works
- users submit smart contracts to Chainlink
- Chainlink has on-chain compontent consisting of 3 main contracts:
    ### 1. Reputation contract
    - keeps track of oracle-service-provider performance metrics 
    ### 2. Order matching contract
    - takes a proposed service level agreement, logs the SLA parameters, and collects bids from oracle providers
    - then selects bids using the reputation contract & finalizes the oracle SLA
    - it also feeds oracle provider metrics back into the reputation contract
    ### 3. Aggregating contract
    - tallies the collective results and calculates a weighted answer
    - validity of each oracle response is then reported to the reputation contract
    - Finally, the weighted answer is returned to the specified contract function in the smart contract that has requested the oracle
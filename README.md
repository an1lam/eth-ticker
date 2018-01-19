# Introduction
In order for the Ethereum ecosystem to mature, contracts will have to be able to retrieve realtime
information about the outside world. Examples of hypothetical contracts that need links to the
outside world include:
- A trading contract that needs currency exchange prices;
- A political prediction market that needs election results; and
- A decentralized real estate title contract that needs to retrieve transaction data from
  government APIs.  

The umbrella term for links between the Ethereum blockchain and the outside world is
[Oracle](https://ethereum.stackexchange.com/questions/11589/how-do-oracle-services-work-under-the-hood).
This project, an Ethereum stock ticker smart contract, provides an oracle-backed API for getting up-to-date stock price
information within the Ethereum blockchain.

# Design
## Smart Contract API
The stock ticker API supports only one function, `getQuote(string symbol)`. `getQuote`, when
called, initiates a query to Alpha Vantage for the most recent stock price of `symbol` and returns
the hash of the transaction that was created. Quotes are reported asynchronously with
`Quote(uint256 priceInUsd, string timestamp)` events.

## Price Source and Updates
Ticker stock prices come from [Alpha Vantage](https://www.alphavantage.co/). When a request from a
client comes in, the ticker contract makes URL requests to [Oraclize](oraclize.it), which forwards
requests to Alpha Vantage's Intraday trading API.

Quotes will represent the price ("open" price for the minute) of stocks as of roughly 1 minute ago.
The contract doesn't make any formal guarantee about how up-to-date prices will be because
timestamps are chosen based on block time. 

## Payment
*TODO: Fill this out.*
- How much gas will this cost me?

# Implementation
## Querying Oraclize and Alpha Vantage
- As a bit of a hack, I'm going to use [ethereum-datetime](https://github.com/pipermerriam/ethereum-datetime)
  to convert the current block time to a datetime string with which I can access a recent price in
  the Alpha Vantage API response.

## Security
- How does the contract hide the Alpha Vantage API key? Is it safe if I make it a private field
  and pass it in the constructor? Read [Vitalik's post](https://blog.ethereum.org/2014/02/08/cryptographic-code-obfuscation-decentralized-autonomous-organizations-are-about-to-take-a-huge-leap-forward/)
  about this.

# Potential Future Improvements
- Enable querying by metric.
- Allow historical or range queries.
- Build my own oracle in Go to eliminate Oraclize.
- Convert the contract from a pull-based to a push-based stocks registry.

# Open Questions
- How can I use Ethereum as a backend and build a simple frontend to query my ticker app? You can
  develop for the Mist browser that the Ethereum team provides.
- Can I make the ticker pluggable so that people can add new query backends? Eh, probably not for
  now.

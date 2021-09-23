# NFT.trades

## **A collaborative approach to NFT reporting**

`nft.trades` is a collaborative effort to make cross-platform NFT trade data available to everyone on Dune Analytics.

2021 saw a lot of new analysts making their firsts dashboard on NFTs, and [Dune Analytics](https://dune.xyz/home) is the place par excellence for fun, creative and cutting-edge NFT analysis.

### **A look at the data in nft.trades**

**NFT Platforms and Marketplaces**

A single row in `nft.trades` documents of the trade of an NFT. The name of trading platform is available in the field `platform`. This is a list of the currently integrated NFT marketplaces and platforms:

* [OpenSea](https://opensea.io/)
* [CryptoPunks](https://www.larvalabs.com/cryptopunks)
* [SuperRare](https://superrare.com/)
* [Rarible](https://rarible.com/)
* [Foundation](https://foundation.app/)

The field `platform_version` indicates the version number for platforms that have more than one iteration of their trading smart contracts such as SuperRare and Rarible. Finally, the column `exchange_contract_address` contains the blockchain address for the platform's trading contract.

**I want to add another platform**

The SQL code that processes the data for every market place is open source and available in our github repository. Everyone can review the code, make pull requests and submit code to add more marketplaces.

**Core Trading data**

**Please, I want to buy your NFT, ser**

A trade is done between a `buyer` and `seller`, both represented by their Ethereum account. A trade can happen in different ways and the platforms SuperRare and Foundation make this info accessible. The `category` field indicates how the trade happened: purchase from list price \('Buy'\), 'Auction Settled' or 'Offer Accepted'.

**What item was traded?**

Key info about the NFT traded is in the fields `nft_contract_address` and `nft_token_id`. If the NFT project is available in the table [nft.tokens](https://github.com/duneanalytics/abstractions/blob/master/ethereum/nft/tokens.sql), then extra info becomes available in the fields `nft_project_name` and `erc_standard`. If you are looking to learn more about NFTs, token IDs and ERC standards, we recommend using the following resource to get started: [The Non-Fungible Token Bible](https://opensea.io/blog/guides/non-fungible-tokens/).

**How much was it traded for?**

The sales amount is accessible in multiple formats including the 'raw' amount with decimals. You will likely need either the `original_amount` denominated in the `original_currency` or you can just ue `usd_amount`.

ETH \(or WETH\) is by far the most used currency in NFT trading on Ethereum. OpenSea and Rarible also support non-ETH trades. Some of the tokens used for these transactions are not in Dune's `prices.usd` \(~ 2% or less\). In these cases, the `usd_amount` is `NULL`.

**Additional data points for advanced use cases**

With the data points discussed so far, you can already do really amazing and sophisticated analysis such as ownership distribution and price floor calculations. But there is more.

One really cool thing about `nft.trades` is the inclusion of ERC721 and ERC1155 `transfer` data for every trade to enable analysis of trades consisting of multiple NFTs.

**Making sense of bundle sales and multi-item trades**

Because a single trade transaction can consist of the transfer of multiple NFTs, we use Postgres [arrays](https://www.postgresql.org/docs/current/functions-array.html) to include ERC721 and ERC1155 `transfer` data:

* `nft_token_ids_array`
* `senders_array`
* `recipients_array`
* `erc_types_array`
* `nft_contract_addresses_array`
* `erc_values_array`

Expanding the values within the arrays into a set of arrays can be done with:

```text
SELECT
    platform,
    tx_hash,
    UNNEST(erc_types_array) AS erc_type,
    UNNEST(nft_token_ids_array) AS token_id,
    UNNEST(nft_contract_addresses_array) AS nft_contract_address
FROM nft.trades
WHERE tx_hash = '\x0142b4beb7bd025a3e2d4e94368098e7589527fee4a6f5cb7f14e1bedbd79f1b'
```

**Making sense of complicated swaps**

The columns `tx_from`, `tx_to` and `trace_address` can sometimes help shed more light on complicated NFT swaps.

**Credits**

Amazing pioneer work on NFTs by the following Dune users:

* [https://dune.xyz/rchen8](https://dune.xyz/rchen8)
* [https://dune.xyz/keeganead](https://dune.xyz/keeganead)
* [https://dune.xyz/eliasimos](https://dune.xyz/eliasimos)

The following power users helped along the way in various ways:

* [https://dune.xyz/0xBoxer](https://dune.xyz/0xBoxer)
* [https://dune.xyz/danner\_eth](https://dune.xyz/danner_eth)

**Get creative with nft.trades and share what you build with it**

Please share your work on Discord or ask for help if you get stuck: [https://discord.gg/ppntYkQu](https://discord.gg/ppntYkQu)

If we like your dashboard, we might tweet about it or include it in the docs. :\)


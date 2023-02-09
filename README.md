# Templates for EVM squids

This repository is a collection of templates for developing [Subsquid](https://www.subsquid.io)-based indexers ("squids") that work with [EVM](https://ethereum.org/en/developers/docs/evm/)-based chains such as Ethereum, Polygon, Arbitrum etc. Available templates:

* [**erc20**](https://github.com/subsquid-labs/squid-erc20-template/) - a complete squid for indexing all standard events and function calls of an arbitrary [ERC20 token contract](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/).
* [**abi**](https://github.com/subsquid-labs/squid-abi-template/) - a template for autogenerating squids that index events and function calls within the [ABI](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html) of a given contract.
* [**evm**](https://github.com/subsquid-labs/squid-evm-template/) - a minimal squid indexing all Ethereum transactions that send ETH to the null address (a.k.a. "burns").

To begin using the templates, install the `sqd` excutable:

```bash
npm install -g @subsquid/cli@latest
```

Next, fetch the chosen template to a new project folder using `sqd init`:
```bash
sqd init <new-project-name> -t <erc20|abi|evm>
```
You now have a copy of the chosen squid template sitting at the `<new-project-name>` folder.

**Note:** at the moment of this writing, the `erc20` template is not yet made available in `sqd init`. If you encounter any issues fetching it, use the following command instead: `sqd init <new-project-name> -t https://github.com/subsquid-labs/squid-erc20-template`.

**Note:** for chains that implement EVM with [Substrate](https://substrate.io) such as Moonbean, Astar or Acala you may want to use one of the [Substrate squid templates](https://github.com/subsquid-labs/squid-substrate-templates).

## Configuring the template

**erc20**: open the `.env` file in the squid project folder and set the `CONTRACT_ADDRESS` variable value to the address of the token contract you want to index. 

**abi**: enter the squid project folder and generate the squid code with `sqd generate`. See `sqd generate --help` for further instructions. The [Archives overview](https://docs.subsquid.net/archives/overview/) page of Subsquid documentation may also be helpful.

**evm**: no configuration required.

## Starting the squid

All EVM squid templates, once configured, are complete squids. They can run locally, provided that `sqd` and Docker are available.

The "processor" process is present in all squids. It downloads pre-filtered blockchain data from [Subsquid Archives](https://docs.subsquid.io/archives/), applies any necessary transformations and saves the result in an [application-appropriate storage](https://docs.subsquid.io/basics/store/). Currently, all template squids store their data to a Postgresql database and provide a way to access it via a GraphQL API.

To start the processor, run
```bash
cd <new-project-name>
npm ci
sqd build
sqd up # starts a Postgres database in a Docker container
sqd process
```
Processor should now be running in foreground, printing messages like
```
01:02:37 INFO  sqd:processor 234354 / 16519081, rate: 17547 blocks/sec, mapping: 2420 blocks/sec, 2945 items/sec, ingest: 794 blocks/sec, eta: 16m
```
The extracted data will begin accumulating in the database available at `localhost:23798` (consult the template's `.env` file for login and password). If you want to access it via GraphQL, run
```bash
sqd serve
```
in a separate terminal in the `<new-project-name>` folder. Graphical query builder will be available at [http://localhost:4350/graphql](http://localhost:4350/graphql).

## Further reading

* [Quickstart guides at Subsquid docs](https://docs.subsquid.io/quickstart/): a more detailed version of this README that gets updated before this file does. In particular:
  - [ABI template page](https://docs.subsquid.io/quickstart/quickstart-abi/)
  - [EVM template page](https://docs.subsquid.io/quickstart/quickstart-ethereum/)
* [Squid development flow](https://docs.subsquid.io/basics/squid-development/): a guide to reshaping a template into your own squid.
* (possibly still under construction)[EVM indexing](https://docs.subsquid.io/evm-indexing/): the comprehensive documentation section on indexing EVM chains.
* [Tutorials](https://docs.subsquid.io/tutorials/) and [examples](https://docs.subsquid.io/examples).

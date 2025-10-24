# rust_ledger_protocol

A simple tutorial for developing a distributed ledger protocol from scratch in Rust.

## What is distributed ledger? How it is implemented? And how it works?

Please read the [step-by-step implementation tutorial](https://gist.github.com/cryptodev/7b945fad3798c9e201649b863d44a892) to get your answers :)

## Instructions to run

Clone the project,

```sh
$ git clone https://github.com/cryptodev/rust_ledger_protocol.git
```

Install the dependencies,

```sh
$ cd rust_ledger_protocol
$ cargo build
```

Start a ledger node server,

```sh
$ export ROCKET_PORT=9000
$ cargo run --bin node_daemon
```

One instance of our ledger node is now up and running at port 9000.

Run the application on a different terminal session,

```sh
$ cargo run --bin client_app
```

The application should be up and running at [http://localhost:7000](http://localhost:7000).

Here are a few screenshots

1. Submitting some transactions

![image.png](https://github.com/cryptodev/rust_ledger_protocol/raw/master/screenshots/1.png)

2. Requesting the node to validate

![image.png](https://github.com/cryptodev/rust_ledger_protocol/raw/master/screenshots/2.png)

3. Resyncing with the ledger for updated data

![image.png](https://github.com/cryptodev/rust_ledger_protocol/raw/master/screenshots/3.png)

To play around by spinning off multiple custom nodes, use the `register_peer/` endpoint to register a new node. 

Here's a sample scenario that you might wanna try,

```sh
# Make sure you set the ROCKET_PORT environment variable before running these nodes
# already running
$ ROCKET_PORT=9000 cargo run --bin node_daemon &
# spinning up new nodes
$ ROCKET_PORT=9001 cargo run --bin node_daemon &
$ ROCKET_PORT=9002 cargo run --bin node_daemon &
```

You can use the following cURL requests to register the nodes at port `9001` and `9002` with the already running `9000`.

```sh
curl -X POST \
  http://127.0.0.1:9001/register_peer \
  -H 'Content-Type: application/json' \
  -d '{"peer_address": "http://127.0.0.1:9000"}'
```

```sh
curl -X POST \
  http://127.0.0.1:9002/register_peer \
  -H 'Content-Type: application/json' \
  -d '{"peer_address": "http://127.0.0.1:9000"}'
```

This will make the node at port 9000 aware of the nodes at port 9001 and 9002, and make the newer nodes sync the ledger with the node 9000, so that they are able to actively participate in the validation process post registration.

To update the node with which the frontend application syncs (default is localhost port 9000), change `CONNECTED_NODE_ADDRESS` field in the [handlers.rs](/app/handlers.rs) file.

Once you do all this, you can run the application, create transactions (submit records via the web interface), and once you validate the transactions, all the nodes in the network will update the ledger. The ledger of the nodes can also be inspected by invoking `/ledger` endpoint using cURL.

```sh
$ curl -X GET http://localhost:9001/ledger
$ curl -X GET http://localhost:9002/ledger
```

*PS: For consulting, you can reach out to me via CodeMentor (use [this link](https://www.codementor.io/cryptodev?partner=cryptodev) for free 15$ credits).*

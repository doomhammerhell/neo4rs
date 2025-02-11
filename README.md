# Neo4rs [![CI Status][ci-badge]][ci-url]  [![Crates.io][crates-badge]][crates-url]

[ci-badge]: https://github.com/neo4j-labs/neo4rs/actions/workflows/checks.yml/badge.svg
[ci-url]: https://github.com/neo4j-labs/neo4rs
[crates-badge]: https://img.shields.io/crates/v/neo4rs.svg?style=shield
[crates-url]: https://crates.io/crates/neo4rs
[docs-badge]: https://img.shields.io/badge/docs-latest-blue.svg?style=shield
[docs-url]: https://docs.rs/neo4rs

Neo4rs is a Neo4j rust driver implemented using [bolt specification](https://7687.org/bolt/bolt-protocol-message-specification-4.html#version-41)

This driver is compatible with Neo4j version 5.x and 4.4.
Only the latest 5.x version is supported, following the [Neo4j Version support policy](https://neo4j.com/developer/kb/neo4j-supported-versions/).

## API Documentation: [![Docs.rs][docs-badge]][docs-url]

## Example

```rust    
    // concurrent queries
    let uri = "127.0.0.1:7687";
    let user = "neo4j";
    let pass = "neo";
    let graph = Arc::new(Graph::new(&uri, user, pass).await.unwrap());
    for _ in 1..=42 {
        let graph = graph.clone();
        tokio::spawn(async move {
            let mut result = graph.execute(
	       query("MATCH (p:Person {name: $name}) RETURN p").param("name", "Mark")
	    ).await.unwrap();
            while let Ok(Some(row)) = result.next().await {
        	let node: Node = row.get("p").unwrap();
        	let name: String = node.get("name").unwrap();
                println!("{}", name);
            }
        });
    }
    
    //Transactions
    let mut txn = graph.start_txn().await.unwrap();
    txn.run_queries(vec![
        query("CREATE (p:Person {name: 'mark'})"),
        query("CREATE (p:Person {name: 'jake'})"),
        query("CREATE (p:Person {name: 'luke'})"),
    ])
    .await
    .unwrap();
    txn.commit().await.unwrap(); //or txn.rollback().await.unwrap();
    
```

## MSRV

The crate has a minimum supported Rust version (MSRV) of `1.60.0`.

A change in the MSRV in *not* considered a breaking change.
For versions past 1.0.0, a change in the MSRV can be done in a minor version increment (1.1.3 -> 1.2.0)
for versions before 1.0.0, a change in the MSRV can be done in a patch version increment (0.1.3 -> 0.1.4).


## License

Neo4rs is licensed under either of the following, at your option:

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT License ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

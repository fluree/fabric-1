## FlureeDB Implementation

`core/ledger/kvledger/txmgmt/statedb/statedb.go` describes the database interface. Hyperledger Fabric handles world states in k-v stores. We can "make" Fluree act as a k-v store, but we should consider how we want to do this (see my comment on `FC-620`). 

The `VersionedDB` interface describes the methods a db is supposed to implement. For example:

`GetState(namespace string, key string)`. 

Whoever works on this may want to look into good VSCode extensions for Golang. `Go to Implementations` and `Go to References` does not find implementation of these interfaces. It looks like those interfaces are implemented in statecouchdb.go. 

I'm starting by mirroring FlureeDB implementation to CouchDB, specifically the package statecouchdb. I don't know if this is the best method, but this codebase is pretty large, so it makes sense for someone who is staying on the project to diver deeper into it. 

The statecouchdb package is made up of the files in `fabric-hyperledger/code/ledger/kvledger/txmgmt/statedb`.

### CouchDB

- couchdb.go

At the top, defining types - types for DB, query results, error results. The interfaces for these types are declared in statedb.go. These are what we would need to implement to get FlureeDB in there. 

At the bottom, a lot of the core db functions - handleRequest.

### Committing
- commit_handling_test.go
- commit_handling.go

Committing has functions that update cache, as well as the db itself. 

### Batch Updates

- batch_util.go

I think we can skip this batch processing for now. Only referenced in metadata_retrieval.go

Actually, if this is just multiple k-v pairs, that can all be one transaction. But in Fluree, if there are updates AND deletions, that will have to be two separate txns. 

### Caching
- cache_test.go
- cache_value.pb.go
- cache_value.proto
- cache.go

I think the cache can pretty much operate the same way for FlureeDB as it does for CouchDB. 

### Testing
- couchdb_test_export.go

Has two functions StartCouchDB (which is referenced in 1 test), which uses the runner to start CouchDB instance (using Docker image).

- couchdb_test.go

Various tests - health check, bad config, etc.



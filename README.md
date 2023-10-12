## distributedCache

### Introduction
- Utilizes the LRU cache eviction algorithm.
- Supports single-machine concurrent caching.
- Implements distributed multiple-node consistent hashing.
- Supports cache defense against cache breakdown.

Create a cache group named "CacheDemo". If the cache doesn't exist, define how to retrieve the data to be cached within distributedCache.GetterFunc(). In this case, we are directly retrieving data from a dictionary:
``` go
func createGroup() *distributedCache.Group {
	return distributedCache.NewGroup("CacheDemo", 2<<10, distributedCache.GetterFunc(
		func(key string) ([]byte, error) {
			log.Println("[SlowDB] search key", key)
			if v, ok := db[key]; ok {
				return []byte(v), nil
			}
			return nil, fmt.Errorf("%s not exist", key)
		}))
}
```
You can start a cache node using `./server -port=8001 &` The default peer cache nodes are:
``` bash
8001
8002
8003
```
If you include the  `-api 1`,parameter, you can start an API server.

To retrieve cached results:
``` bash
# http://localhost:9999/api?key=JL
```
If the cache contains the data, it will be returned. If not, it will be retrieved from the slow database, and if the slow database does not contain the data, an empty response will be returned.


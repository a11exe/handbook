# Redis

Remote dictionary service. 
The open-source, **in-memory data store** used by millions of developers as a cache, vector database, document database, 
streaming engine, and message broker.

## Redis persistence
How Redis writes data to disk

Persistence refers to the writing of data to durable storage, such as a solid-state disk (SSD). Redis provides a range of persistence options. These include:

+ **RDB (Redis Database)**: RDB persistence performs point-in-time snapshots of your dataset at specified intervals.
+ **AOF (Append Only File)**: AOF persistence logs every write operation received by the server. These operations can then be replayed again at server startup, reconstructing the original dataset. Commands are logged using the same format as the Redis protocol itself.
+ **No persistence**: You can disable persistence completely. This is sometimes used when caching.
+ **RDB + AOF**: You can also combine both AOF and RDB in the same instance.

## Run Redis in Docker
```bash
docker run --name my-redis -d redis  
docker exec -it my-redis redis-cli
```

## Main commands
```bash
PING # PONG
SET Car Ferrari # Add key
GET Car # Get key
GET AbsentKey # (nil)
DEL Car # Delete key
SET my-cache 143 EX 10 # Key will be deleted after 10 seconds
KEYS * # get all keys
SADD fruits apple # create list fruits and adding apple
SADD fruits banana lime # adding banana and lime
SMEMBERS fruits
1) "apple"
2) "banana"
3) "lime"
SREM fruits banana # remove from list banana
DEL fruits # remove key fruits
```


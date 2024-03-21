# Redis Clients

- Manage Connections
- Impl the Redis wire protocol(RESP) REdis Serialization Protocol(RESP).
- provide a usable language-specific API
- for JAVA:
  [Reddison, Jedis, Lettuce(NIO)]
  ***


    # Jedis
    - Jedis instance is not a thread safe, JedisPool provide u Jedis instance in a thread safe manner.
    - get Jedis instance from a JedisPool in context of try-with resource.
    - when configuring JedisPool u provide
       [host, port, JedisPoolConfig instance( a wrapper around the Apache Common generic object pool )]
       - max connec def : 8.
    - there a few other options to connect to redis depending on ur deployment type:
      - RedisCluster - Redis Sentinel

    - LREM, SMEMBERS are O(N)
      - for larger sets use SSCAN over SCARD

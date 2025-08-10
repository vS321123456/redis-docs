# Redis Data Types

Redis is an in-memory data structure store, commonly used as a database, cache, and message broker. It supports a variety of data types to meet different use cases.

## 1. Strings
- **Description**: The most basic Redis data type.
- **Usage**: Can store any kind of data, such as text, integers, or serialized objects.
- **Commands**:
  - `SET key value`
  - `GET key`
  - `INCR key`
  - `APPEND key value`

## 2. Lists
- **Description**: Ordered collections of strings.
- **Usage**: Used for queues, message queues, etc.
- **Commands**:
  - `LPUSH key value`
  - `RPUSH key value`
  - `LPOP key`
  - `LRANGE key start stop`

## 3. Sets
- **Description**: Unordered collections of unique strings.
- **Usage**: Used to store unique elements, like tags or user IDs.
- **Commands**:
  - `SADD key member`
  - `SREM key member`
  - `SMEMBERS key`
  - `SINTER key1 key2`

## 4. Sorted Sets (ZSets)
- **Description**: Sets ordered by a score (floating-point number).
- **Usage**: Useful for leaderboards, ranking systems.
- **Commands**:
  - `ZADD key score member`
  - `ZRANGE key start stop`
  - `ZRANK key member`
  - `ZREM key member`

## 5. Hashes
- **Description**: Maps between string field and string value, like a dictionary.
- **Usage**: Good for representing objects (e.g., a user profile).
- **Commands**:
  - `HSET key field value`
  - `HGET key field`
  - `HGETALL key`
  - `HDEL key field`

## 6. Bitmaps
- **Description**: Bit-level operations on strings.
- **Usage**: Efficient for storing flags or tracking user activity.
- **Commands**:
  - `SETBIT key offset value`
  - `GETBIT key offset`
  - `BITCOUNT key`

## 7. HyperLogLogs
- **Description**: Probabilistic data structure for counting unique items.
- **Usage**: Approximate cardinality estimation.
- **Commands**:
  - `PFADD key element`
  - `PFCOUNT key`
  - `PFMERGE destkey sourcekey`

## 8. Streams
- **Description**: Log-like data structure for storing a sequence of messages.
- **Usage**: Event sourcing, message queues.
- **Commands**:
  - `XADD key * field value`
  - `XRANGE key start end`
  - `XREAD COUNT n STREAMS key id`

## Conclusion
Redis offers a rich set of data types to support a variety of use cases. Choosing the right data type is essential for optimal performance and maintainability.


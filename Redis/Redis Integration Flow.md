# Redis Cache Integration Documentation

## Overview

This document describes the Redis cache integration implemented in the CacheService application. The integration provides both in-memory caching for high performance and Redis caching for external access and data persistence.

## Architecture

The caching solution follows a **hybrid approach**:
- **In-memory cache**: For high-performance local access within the application
- **Redis cache**: For external access by other Spring MVC applications and data persistence

## Key Components

### 1. Redis Configuration (`RedisConfig.java`)
- Configures Redis connection using Jedis
- Sets up cache managers with different TTL configurations
- Configures JSON serialization for complex objects

### 2. Redis Cache Service (`RedisCacheService.java`)
- Handles all Redis operations
- Provides methods for caching, retrieval, and eviction
- Implements error handling and logging

### 3. Cache Management Controller (`CacheManagementController.java`)
- REST endpoints for cache management operations
- Provides cache health monitoring
- Enables cache eviction and status checking

## Cache Types and TTL Configuration

| Cache Type | TTL | Redis Key | Description |
|------------|-----|-----------|-------------|
| Master Data | 24 hours | cache:appParams | Application parameters and configurations |
| Offer Product | 12 hours | cache:offerProduct | Product offerings and mappings |
| Payment Type | 48 hours | cache:paymentTypeMaster | Payment type configurations |
| Rate Plan | 24 hours | cache:ratePlanMap | Rate plan information |
| Search Module | 24 hours | cache:searchModuleMap | Search module configurations |

## Configuration

### Redis Connection (application.yml)
```yaml
spring:
  redis:
    host: localhost
    port: 6379
    database: 0
    timeout: 2000ms
    jedis:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0
        max-wait: -1ms
  cache:
    type: redis
    redis:
      time-to-live: 86400000  # 24 hours in milliseconds
      cache-null-values: false
```

### Dependencies Added
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

## API Endpoints

### Cache Management Endpoints

#### 1. Evict All Cache
```http
DELETE /cache/management/evictAll
```
Evicts all cache entries from Redis.

#### 2. Evict Master Data Cache
```http
DELETE /cache/management/evictMasterData
```
Evicts only master data cache entries.

#### 3. Evict Offer Product Cache
```http
DELETE /cache/management/evictOfferProduct
```
Evicts only offer product related cache entries.

#### 4. Evict Specific Cache
```http
DELETE /cache/management/evict/{cacheKey}
```
Evicts a specific cache entry by key.

#### 5. Cache Status
```http
GET /cache/management/status
```
Returns the status of all cache entries including existence and TTL information.

#### 6. Check Cache Existence
```http
GET /cache/management/exists/{cacheKey}
```
Checks if a specific cache key exists and returns its TTL.

#### 7. Health Check
```http
GET /cache/management/health
```
Performs a health check on Redis connectivity.

### Existing Cache Refresh Endpoints

#### 1. Full Cache Refresh
```http
GET /cache/refresh
```
Refreshes all cache data from database and updates both in-memory and Redis caches.

#### 2. Refresh by Product Code
```http
GET /cache/refreshCacheByProductCode?productCode={productCode}
```
Refreshes cache for a specific product code.

#### 3. Refresh Master Data
```http
GET /cache/refreshMDCache
```
Refreshes only master data cache entries.

## Data Access

The cache service now supports dual access:

1. **Internal Application Access**: Uses in-memory cache for maximum performance
2. **External Application Access**: Other Spring MVC applications can access data via REST endpoints

### For External Applications

External applications can access cached data through the existing REST endpoints:

```http
GET /cache/getString?key={key}&subKey={subKey}
GET /cache/getOfferProductMapList
GET /cache/getPaymentTypeMaster
GET /cache/getPriceSubTypeMaster
GET /cache/getSalesNameMap
GET /cache/getSubTypeNameMap
GET /cache/getMap?key={key}
```

## Cache Refresh Mechanisms

### 1. Full Refresh
- Triggered manually via `/cache/refresh` endpoint
- Evicts all Redis cache before refresh
- Reloads all data from Oracle database
- Updates both in-memory and Redis caches

### 2. Incremental Refresh
- Triggered via timestamp-based mechanism
- Updates only changed data
- Maintains cache consistency

### 3. Product-Specific Refresh
- Triggered via `/cache/refreshCacheByProductCode` endpoint
- Updates only specific product data
- Efficient for targeted updates

## Error Handling

The implementation includes comprehensive error handling:

1. **Redis Connection Failures**: Application continues with in-memory cache only
2. **Cache Eviction Errors**: Logged and reported via API responses
3. **Serialization Errors**: Handled gracefully with proper logging

## Monitoring and Logging

- All cache operations are logged using SLF4J
- Cache hit/miss information is available
- TTL and cache existence can be monitored via management endpoints
- Health check endpoint provides Redis connectivity status

## Best Practices

### 1. Cache Key Naming Convention
All Redis keys follow the pattern: `cache:{dataType}`

### 2. TTL Strategy
- Master data: 24-48 hours (relatively static)
- Offer products: 12 hours (moderate changes)
- Application parameters: 6 hours (frequent changes)

### 3. Error Handling
- Always handle Redis connection failures gracefully
- Fallback to database queries when cache is unavailable
- Log all cache-related errors for monitoring

### 4. Memory Management
- Use appropriate TTL values to prevent memory bloat
- Regularly monitor Redis memory usage
- Implement cache eviction policies as needed

## Deployment Considerations

### 1. Redis Server Setup
- Ensure Redis server is running and accessible
- Configure appropriate memory limits
- Set up Redis persistence if needed
- Consider Redis clustering for high availability

### 2. Network Configuration
- Ensure proper network connectivity between application and Redis
- Configure firewall rules if necessary
- Use Redis AUTH if security is required

### 3. Monitoring
- Monitor Redis memory usage
- Track cache hit rates
- Set up alerts for Redis connectivity issues
- Monitor application logs for cache-related errors

## Troubleshooting

### Common Issues

1. **Redis Connection Refused**
   - Check if Redis server is running
   - Verify host and port configuration
   - Check network connectivity

2. **Serialization Errors**
   - Verify entity classes are serializable
   - Check Jackson configuration
   - Ensure compatible data types

3. **Memory Issues**
   - Monitor Redis memory usage
   - Adjust TTL values if needed
   - Implement cache eviction policies

4. **Performance Issues**
   - Monitor cache hit rates
   - Check network latency to Redis
   - Consider Redis clustering for scale

### Debugging

Enable debug logging for cache operations:
```yaml
logging:
  level:
    com.jio.cache.service.RedisCacheService: DEBUG
    org.springframework.data.redis: DEBUG
```

## Future Enhancements

1. **Cache Warming**: Implement automatic cache warming on application startup
2. **Cache Statistics**: Add detailed cache statistics and metrics
3. **Distributed Locking**: Implement distributed locking for cache updates
4. **Cache Compression**: Add data compression for large cache entries
5. **Multi-Region Support**: Support for Redis clusters across multiple regions

## Support and Maintenance

- Regular monitoring of cache performance
- Periodic review of TTL configurations
- Database and Redis version compatibility checks
- Performance tuning based on usage patterns

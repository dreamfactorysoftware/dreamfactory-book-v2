---
title: "DreamFactory Performance Tuning: Caching, Scaling & Optimization"
linkTitle: "Performance Considerations"
description: "Optimize DreamFactory performance with caching (Redis, Memcached), database connection pooling, rate limiting, and horizontal scaling strategies for enterprise deployments."
weight: 11
---

DreamFactory is a PHP-based application, and while we work hard to optimize the code at every opportunity, performance is going to largely be dictated by decisions made at the infrastructure level. Fortunately, these decisions are not so much dictated by budget as by sound technology and hosting choices. While your DreamFactory environment will undoubtedly be more performant on for instance an AWS t2.large than on a $15 Digital Ocean Droplet, the chasm between the two can be dramatically reduced when care is taken to properly configure and tune the environment. In this chapter we'll provide some general performance benchmarks, and then provide guidance how to ensure your DreamFactory instance is running at peak capacity.

## Performance Benchmarks

The following table presents DreamFactory’s average response time in association with hosting the platform on various popular hosting solutions. In each case the hosting environments were unmodified, and not optimized in any fashion. It is however important to note all are running PHP 7.2, NGINX, and PHP-FPM.

| Environment               | Load and API Type                                                | Average Response Time     |
|---------------------------|------------------------------------------------------------------|---------------------------|
| $15 Digital Ocean Droplet | 10 MySQL API requests/second (10 records)                        | 2524 ms                   |
| $15 Digital Ocean Droplet | 10 MySQL API requests/second with caching enabled (10 records)   | 101 ms (96% improvement)  |
| $15 Digital Ocean Droplet | 10 MySQL API requests/second with caching enabled (100 records)  | 145ms                     |
| AWS t2.large              | 50 MySQL API requests/second (100 records)                       | 83 ms                     |
| AWS t2.large              | 50 MySQL API requests/second with caching enabled (100 records)  | 72 ms (13.3% improvement) |
| AWS t2.large              | 100 MySQL API requests/second (100 records)                      | 85 ms                     |
| AWS t2.large              | 100 MySQL API requests/second with caching enabled (100 records) | 73 ms (14.2% improvement) |
| AWS t2.large              | 10 S3 API JPEG file requests/second                              | 198 ms                    |
| AWS t2.large              | 10 MySQL API requests/second (1,000 records)                     | 281 ms                    |
| AWS t2.large              | 10 MySQL API requests/second with caching enabled (1,000 records)| 264 ms (7.2% improvement) |

These load tests were carried out using the third-party load testing service [Loader.io.](https://loader.io).

The clearest takeaway here is that enabling database caching can have a tremendous impact on performance in the event you’re running DreamFactory on a low-powered server. In more robust server environments the impact isn’t as stark, however all the same enabling caching on the AWS t2.large produced on average a 11.6% performance improvement for the scenarios cited above. So what else can you do to improve performance?

## Optimizing DreamFactory's Database APIs

Ensuring the DreamFactory-generated database APIs are running at peak performance is accomplished by ensuring your database is properly configured, has been allocated appropriate hardware and network resources, and turning on DreamFactory's database caching feature. In this section we'll talk more about all of these tasks.

### Index the Database

For database-backed APIs, there is no more impactful task one could take than properly indexing the database. Database indexing is what allows your database engine to quickly identify which rows match conditions defined by a `where` clause. Refer to the following resources for both general and database-specific indexing information:

* [Database Indexes Defined](https://en.wikipedia.org/wiki/Database_index)
* [Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/indexes?view=sql-server-2017)
* [MongoDB](https://docs.mongodb.com/manual/indexes/)
* [MySQL](https://dev.mysql.com/doc/refman/5.7/en/optimization-indexes.html)
* [Oracle](https://docs.oracle.com/cd/E11882_01/server.112/e40540/indexiot.htm#CNCPT721)
* [PostgreSQL](https://www.postgresql.org/docs/9.1/indexes.html)

### Database API Caching

Enable database API caching whenever practical, as it will undoubtedly improve performance.

<img src="/images/10/database_caching.png" width="800" alt="Enable Database API Caching">

DreamFactory instances may be load balanced, and can be configured to share the system database, cache details, and other information necessary to operate in a distributed environment. Below are some links that may help you configure a load balancer with some of the most common cloud providers.

1. [Amazon Web Services](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancer-getting-started.html)
2. [Google Cloud](https://cloud.google.com/load-balancing/)
3. [Microsoft Azure](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview)
4. [IBM Cloud](https://www.ibm.com/cloud/load-balancer)

DreamFactory enables file-based caching by default, however you may opt to configure one of the other supported caching solutions, such as Redis. Please see these links to see connection tutorials:

 1. [YouTube - Setting up and using Redis](https://www.youtube.com/watch?v=2Xp_3oBcYXo)
 2. [Blog - Caching](https://blog.dreamfactory.com/new-dreamfactory-cache-service-supports-redis-memcahed-and-local-storage/)

### Connection Pooling with Oracle

If you are using an Oracle database, you may see performance improvements by setting up Connection Pooling. This should reduce the number of database connections being made. To do so with your DreamFactory Oracle connector, simple add `PDO::ATTR_PERSISTENT` `true` as a driver option on the configuration tab of your Oracle Service.

 ## Adding Redis Caching

One of DreamFactory's great advantages is it is built atop Laravel, and as such, you can take advantage of Laravel's support for shared caching solutions, among other things. This is great because it means the caching solution has been extensively tested and proven in production environments.

To install the predis package you just need to navigate to your project's root directory and execute this command:

    $ composer require predis/predis

Next, open your .env file and look for this section:

    # CACHE_DRIVER options: apc, array, database, file, memcached, redis
    CACHE_DRIVER=file

    Change CACHE_DRIVER to:

    CACHE_DRIVER=redis

Next, scroll down and uncomment these lines by removing the `#`, and then update the `CACHE_HOST`, `CACHE_PORT`, and (optionally) the `CACHE_PASSWORD` parameters to match your Redis environment:

    # If CACHE_DRIVER = memcached or redis
    #CACHE_HOST=
    #CACHE_PORT=
    #CACHE_PASSWORD=

Finally, scroll down to the following section and uncomment `CACHE_DATABASE` and `REDIS_CLIENT`:

    # If CACHE_DRIVER = redis
    #CACHE_DATABASE=2
    # Which Redis client to use: predis or phpredis (PHP extension)
    #REDIS_CLIENT=predis

You can leave `CACHE_DATABASE` set to 2. For the `REDIS_CLIENT` you can leave it set to predis if you've installed the predis/predis package (recommended). By default your Redis Database will be on 0, so be sure to `SELECT` whatever the number is you have set your `CACHE_DATABASE` equal to. Then you can start seeing the `KEYS` populate.

## Load Balancing Your DreamFactory Environment

You can use a load balancer to distribute API requests among multiple servers. A load balancer can also perform health checks and remove an unhealthy server from the pool automatically. Most large server architectures include load balancers at several points throughout the infrastructure. You can cluster load balancers to avoid a single point of failure. DreamFactory is specifically designed to work with load balancers and all of the various scheduling algorithms. A REST API request can be sent to any one of the web servers at any time and handled in a stateless manner.

The following diagram depicts a typical load-balanced, high-availability environment. Like many HTTP-based applications, DreamFactory can be scaled using a three tier architecture:

* Application tier: These are the servers where one or more DreamFactory instances operate.
* Caching tier: If you load balance DreamFactory across multiple servers, you will need to use a caching solution such as Redis or Memcached for distributed cache management.
* Database tier: In a load balanced environment the DreamFactory system database will need to be shared across the instances. You can use MySQL, PostgreSQL, or Microsoft SQL Server for this purpose.

<img src="/images/performance/load-balanced-diagram.png" alt="Running DreamFactory in a Load Balanced Environment">

## Compiling the DreamFactory Code with OPcache

You can achieve particularly high performance by compiling your DreamFactory application code using OPcache. The following

1. [PHP's OPcache Documentation](https://php.net/manual/en/book.opcache.php)
2. [How to Make your Laravel App Fly](https://medium.com/appstract/make-your-laravel-app-fly-with-php-opcache-9948db2a5f93)

## Third-party Resources

* ["NGINX Cookbook: Advanced Recipes for High Performance Load Balancing"](https://www.nginx.com/resources/library/complete-nginx-cookbook/): This 175 page guide offers deep insights into and concrete solutions regarding NGINX scaling.
* [Amazon EC2 cost calculator](https://calculator.s3.amazonaws.com/): This calculator can help you navigate AWS' notoriously opaque EC2 instance pricing model.

## Conclusion

These are just a few tips intended to help you ensure your DreamFactory environment is running at peak capacity! If you have other ideas, please send them to code AT dreamfactory.com!


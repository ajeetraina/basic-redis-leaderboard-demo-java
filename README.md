# Basic Redis Leaderboard Demo Java

Show how the redis works with Java, Spring.

## Try it out

<p>
    <a href="https://heroku.com/deploy" target="_blank">
        <img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy to Heorku" width="200px"/>
    <a>
</p>

<p>
    <a href="https://deploy.cloud.run" target="_blank">
        <img src="https://deploy.cloud.run/button.svg" alt="Run on Google Cloud" width="200px"/>
    </a>
    (See notes: How to run on Google Cloud)
</p>


## How to run on Google Cloud

<p>
    If you don't have redis yet, plug it in  (https://spring-gcp.saturnism.me/app-dev/cloud-services/cache/memorystore-redis).
    After successful deployment, you need to manually enable the vpc connector as shown in the pictures:
</p>

1. Open link google cloud console.

![1 step](docs/1.png)

2. Click "Edit and deploy new revision" button.

![2 step](docs/2.png)

3. Add environment.

![3 step](docs/3.png)

4.  Select vpc-connector and deploy application.

![4  step](docs/4.png)

<a href="https://github.com/GoogleCloudPlatform/cloud-run-button/issues/108#issuecomment-554572173">
Problem with unsupported flags when deploying google cloud run button
</a>

## How it works?

![How it works](docs/screenshot001.png)


# How it works?
## 1. How the data is stored:
<ol>
    <li>The AAPL's details - market cap of 2,6 triillions and USA origin - are stored in a hash like below:
      <pre> <a href="https://redis.io/commands/hset">HSET</a> "company:AAPL" symbol "AAPL" market_cap "2600000000000" country USA</pre>
     </li>
    <li>The Ranks of AAPL of 2,6 trillions are stored in a <a href="https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/1-2-what-redis-data-structures-look-like/1-2-5-sorted-sets-in-redis/">ZSET</a>. 
      <pre><a href="https://redis.io/commands/zadd">ZADD</a>  companyLeaderboard 2600000000000 company:AAPL</pre>
    </li>
</ol>

<br/>

## 2. How the data is accessed:
<ol>
    <li>Top 10 companies: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 0 9 WITHSCORES</pre> </li>
    <li>All companies: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 0 -1 WITHSCORES</pre> </li>
    <li>Bottom 10 companies: <pre><a href="https://redis.io/commands/zrange">ZRANGE</a> companyLeaderboard 0 9 WITHSCORES</pre></li>
    <li>Between rank 10 and 15: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 9 14 WITHSCORES</pre></li>
    <li>Show ranks of AAPL, FB and TSLA: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a>  companyLeaderBoard company:AAPL company:FB company:TSLA</pre> </li>
    <!-- <li>Pagination: Show 1st 10 companies: <pre><a href="https://redis.io/commands/zscan">ZSCAN</a> 0 companyLeaderBoard COUNT 10 7.Pagination: Show next 10 companies: ZSCAN &lt;return value from the 1st 10 companies&gt; companyLeaderBoard COUNT 10 </li> -->
    <li>Adding 1 billion to market cap of FB company: <pre><a href="https://redis.io/commands/zincrby">ZINCRBY</a> companyLeaderBoard 1000000000 "company:FB"</pre></li>
    <li>Reducing 1 billion of market cap of FB company: <pre><a href="https://redis.io/commands/zincrby">ZINCRBY</a> companyLeaderBoard -1000000000 "company:FB"</pre></li>
    <li>Companies between 500 billion and 1 trillion: <pre><a href="https://redis.io/commands/zcount">ZCOUNT</a> companyLeaderBoard 500000000000 1000000000000</pre></li>
    <li>Companies over a Trillion: <pre><a href="https://redis.io/commands/zcount">ZCOUNT</a> companyLeaderBoard 1000000000000 +inf</pre> </li>
</ol>


## How to run it locally?

## Development

```
git clone 
```

### Run docker compose or install redis manually

Install docker (on mac: https://docs.docker.com/docker-for-mac/install/)

```sh
docker network create global
docker-compose up -d --build
```

```
 docker-compose ps
            Name                           Command               State             Ports          
--------------------------------------------------------------------------------------------------
redis.redisleaderboard.docker   docker-entrypoint.sh redis ...   Up      127.0.0.1:55000->6379/tcp

```

#### copy .env.example to create .env (copy .env.example .env  or cp .env.example .env) . And provide the values for environment variables (if needed)
   	- REDIS_HOST: Redis server host
	- REDIS_PORT: Redis server port
	- REDIS_DB: Redis server db index
	- REDIS_PASSWORD: Redis server password

```
REDIS_URL=
REDIS_HOST=redis://localhost
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=
```


#### Run backend

Install gradle (on mac: https://gradle.org/install/)

```
brew install gradle
```


Install JDK (on mac: https://docs.oracle.com/javase/10/install/installation-jdk-and-jre-macos.htm)

``` sh
export $(cat .env | xargs)
```

```
gradle wrapper
```
```
gradle wrapper

Welcome to Gradle 6.8.3!

Here are the highlights of this release:
 - Faster Kotlin DSL script compilation
 - Vendor selection for Java toolchains
 - Convenient execution of tasks in composite builds
 - Consistent dependency resolution

For more details see https://docs.gradle.org/6.8.3/release-notes.html

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 29s
1 actionable task: 1 executed
```

```
./gradlew build
```
```
% ./gradlew build
Downloading https://services.gradle.org/distributions/gradle-6.8.3-bin.zip
..........10%..........20%..........30%...........40%..........50%..........60%..........70%...........80%..........90%..........100%
Starting a Gradle Daemon, 1 incompatible Daemon could not be reused, use --status for details

> Task :test
2021-03-01 07:08:42.962  INFO 3624 --- [extShutdownHook] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'

BUILD SUCCESSFUL in 1m 13s
12 actionable tasks: 12 executed
ajeetraina@Ajeets-MacBook-Pro basic-redis-leaderboard-demo-java %
```

```
./gradlew run
```

```
./gradlew run

> Task :run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.1)

2021-03-01 07:09:59.610  INFO 3672 --- [  restartedMain] BasicRedisLeaderLoardDemoJavaApplication : Starting BasicRedisLeaderLoardDemoJavaApplication using Java 13.0.2 on Ajeets-MacBook-Pro.local with PID 3672 (/Users/ajeetraina/projects/basic-redis-leaderboard-demo-java/build/classes/java/main started by ajeetraina in /Users/ajeetraina/projects/basic-redis-leaderboard-demo-java)
2021-03-01 07:09:59.614  INFO 3672 --- [  restartedMain] BasicRedisLeaderLoardDemoJavaApplication : No active profile set, falling back to default profiles: default
2021-03-01 07:09:59.661  INFO 3672 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
2021-03-01 07:09:59.661  INFO 3672 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : For additional web related logging consider setting the 'logging.level.web' property to 'DEBUG'
2021-03-01 07:10:00.481  INFO 3672 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 5000 (http)
2021-03-01 07:10:00.492  INFO 3672 --- [  restartedMain] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-03-01 07:10:00.492  INFO 3672 --- [  restartedMain] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.41]
2021-03-01 07:10:00.551  INFO 3672 --- [  restartedMain] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-03-01 07:10:00.551  INFO 3672 --- [  restartedMain] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 889 ms
2021-03-01 07:10:00.756  INFO 3672 --- [  restartedMain] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-03-01 07:10:00.845  INFO 3672 --- [  restartedMain] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page: URL [file:/Users/ajeetraina/projects/basic-redis-leaderboard-demo-java/assets/index.html]
2021-03-01 07:10:00.949  INFO 3672 --- [  restartedMain] .s.s.UserDetailsServiceAutoConfiguration : 

Using generated security password: ea2d5326-b04c-4f93-b771-57bcb53f656e

2021-03-01 07:10:01.016  INFO 3672 --- [  restartedMain] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with [org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@583fa06c, org.springframework.security.web.context.SecurityContextPersistenceFilter@524c0386, org.springframework.security.web.header.HeaderWriterFilter@c6e5d4e, org.springframework.security.web.authentication.logout.LogoutFilter@3e1f33e9, org.springframework.security.web.savedrequest.RequestCacheAwareFilter@6790427f, org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@40ddf86, org.springframework.security.web.authentication.AnonymousAuthenticationFilter@1412ffa9, org.springframework.security.web.session.SessionManagementFilter@3eb6c20f, org.springframework.security.web.access.ExceptionTranslationFilter@21646e94, org.springframework.security.web.access.intercept.FilterSecurityInterceptor@649e1b25]
2021-03-01 07:10:01.043  INFO 3672 --- [  restartedMain] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2021-03-01 07:10:01.065  INFO 3672 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 5000 (http) with context path ''
2021-03-01 07:10:01.093  INFO 3672 --- [  restartedMain] BasicRedisLeaderLoardDemoJavaApplication : Started BasicRedisLeaderLoardDemoJavaApplication in 1.937 seconds (JVM running for 2.327)
<=========----> 75% EXECUTING [17s]
> :run

```




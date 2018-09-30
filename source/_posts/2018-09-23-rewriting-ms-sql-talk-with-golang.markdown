---
layout: post
title: "Rewriting an MS SQL gateway with Golang"
date: 2018-09-23 05:51
comments: true
categories: [mssql]
author: Ming Hu
---

Innovation happens at an increasing rate today, which means code that is only one
or two years old might become outdated and hard to maintain.
In Namshi, we also face issues caused by legacy code. When an app starts to lag or break,
the team gathers and discusses a best approach to fix issues. Sometimes we do an incremental
refactor and other times we go for a complete rewrite.
This month we rewrote an app from scratch, and here is how we did it.

### Why?

Namshi runs an app that acts as a gateway in front of an MS SQL server. We recently moved our MS SQL server to a different cloud provider, and our MS SQL gateway started to get stuck (taking more than 10 seconds to respond), causing slow operations in the apps relying on the gateway. We received daily (and nightly) calls due to slow response and needed to restart the app quite often. The limited amount of logging also made it hard for us to pinpoint the bottleneck. The app was also written in C#, a less used language in our team, and requires more attention.

Refactoring the code gives us the ease of not needing to go through a full development and testing cycle. However, the app might still get stuck and take huge effort to debug and maintain. On the other hand, a complete rewrite will improve stability, the logging system as well as easier performance management.

Considering the benefits of each approach, we decided to give it a complete rewrite.

### How?

First we went out scouting for a driver. The driver we started with was the [Node.JS driver](https://www.npmjs.com/package/mssql). It was easy to use. However, it requires to specify SQL variable type when we create prepared statement. Our existing queries do not specify the SQL type for parameters, so it's painful to add all the fields. So, we decided to opt for a second choice, the [Golang driver](https://github.com/denisenkom/go-mssqldb). Golang has been popular in the backend team. We love it for its simplicity, performance, concurrency as well as its rapid development and growing community. Check out below the difference of creating prepared statement with NodeJS and Golang drivers:

```javascript
/* Node driver: create prepared statements */
const ps = new sql.PreparedStatement(/* [pool] */)
/* We need to specify the SQL type of parameters */
ps.input('param', sql.Int)
ps.prepare('select @param as value', err => {
    // ... error checks
    ps.execute({param: 12345}, (err, result) => {
        // ... error checks
        ps.unprepare(err => {
            // ... error checks
        })
    })
})
```

```go
// Golang driver: create prepared statements
// We don't need to specify the SQL type of parameters
db.QueryContext(ctx, `select * from t where ID = @ID and Name = @p2;`, sql.Named("ID", 6), "Bob")
```

To run an MS SQL Server locally, we used this [Docker Image] (https://hub.docker.com/r/microsoft/mssql-server-linux/) and created a test database for rapid prototyping. Our first snippet of code only had a single function to execute a dummy query against the database.

From there, we started implementing the app as per the old `README`. We had to battle with taking care of data types (e.g, casting DECIMAL to float, or formatting dates correctly for MSSQL), use transactions in write queries and use connection pooling to enhance performance. For logging, we add logs for the time each query takes and the specific parameters each query uses. It becomes much easier for future troubleshooting and debugging.

### Rollout

Rolling out a critical app requires careful planning. To start with, we rolled out the apps on staging and ran test queries to make sure everything worked fine. Then we switched a few live apps on a separate service and kept them running for a period of time. After couple days, we rolled out more live apps and fixed bugs as they came. A week later, we switched all live apps and checked logs closely to make sure all went well.

### Outcome

With this rewrite we achieved much better performance! The integration with New Relic lets us check app performance in real time and figure out what is causing performance issue. Detailed logging allows us to debug and improve code rapidly. More importantly, this new app is well understood by the team and has been very stable since we switched. We are not receiving daily or nightly calls any more! :)

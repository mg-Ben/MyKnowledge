---
tags:
  - Others
---
# Core principles
It is a [[Framework]], i.e. a set of tools and libraries that let users monitor and instrument [[#Telemetry data]] for applications (such as [[Linux Service|Linux Services]]).
## Example
One example of _Open Telemetry_ is the `@opentelemetry` package for [[NodeJS]], which can be installed through [[NodeJS#npm|npm]] to scrape traces, metrics and logs from some server.
To implement `@opentelemetry`, it is necessary to create some configuration and then import the configuration in the code with a `require` or `import` statement.
## Telemetry data
It involves:
- **Metrics**: CPU usage, memory usage, system performance
- **Logs**: events over time, used for debugging purposes
- **Traces**: path and performance of requests as they pass through various services

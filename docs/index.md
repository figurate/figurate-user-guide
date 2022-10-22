# Introduction

Figurate is a collection of patterns and concerns relevant to the operation of modern software
systems. Figurate promotes the design principles of modularity and
[separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns), such that the
cognitive load of managing individual software components is reduced.

Whilst modularity and loose coupling are recognised as good tenents of software design, it
is also just as valid when considering the wider DevOps ecosystem. Whether you look towards
Platform Engineering, Golden Path or something else, these are all just implementations of
separation of concerns and loose coupling.


<style>
    .heatMap {
        text-align: center;
    }
    .heatMap a {
        color: white;
    }
    .heatMap th {
        background: grey;
        word-wrap: break-word;
        text-align: center;
    }
    .heatMap tr {
        background: silver;
    }
    .heatMap tr:nth-child(1) td:nth-child(1) { background: red; }
    .heatMap tr:nth-child(1) td:nth-child(2) { background: orange; }
    .heatMap tr:nth-child(1) td:nth-child(3) { background: green; }
    .heatMap tr:nth-child(2) td:nth-child(1) { background: orange; }
    .heatMap tr:nth-child(2) td:nth-child(2) { background: green; }
    .heatMap tr:nth-child(3) td:nth-child(1) { background: green; }
</style>

<div class="heatMap">

| Security                          | Reliability                        | Efficiency                            | Operational Excellence          | Cost Optimization           | Sustainability        |
|-----------------------------------|------------------------------------|---------------------------------------|---------------------------------|-----------------------------|-----------------------|
| [Tenant management](TENANCY.md)   | [Identity Management](identity.md) | [Permissions](PERMISSIONS.md)         | [Certificates](CERTIFICATES.md) | [Encryption](ENCRYPTION.md) | [Secrets](SECRETS.md) |
| [Threats](threats.md)             | [Audit](AUDIT.md)                  | [Usage monitoring](USAGE.md)          | [Throttling](THROTTLING.md)     | [Backups](BACKUP.md)        | -                     |
| [Logging](LOGGING.md)             | [Routing](ROUTING.md)              | [Vulnerabilities](VULNERABILITIES.md) | [Service Quotas](QUOTAS.md)     | -                           | -                     |
| [Monitoring](MONITORING.md)       | [Networking](NETWORKING.md)        | [Serverless](SERVERLESS.md)           | -                               | -                           | -                     |
| [Alerting](ALERTING.md)           | [Tracing](TRACING.md)              | -                                     | -                               | -                           | -                     |
| [Observability](OBSERVABILITY.md) | -                                  | -                                     | -                               | -                           | -                     |

</div>

Beginning with the top left of the Figurate table, and working across and down, we can
address each of the concerns specific to an application, platform or organisation.
Where the application(s) are hosted is an obvious primary concern, so we consider tenancy 
management as a starting point for any workload.

Identity is also an important concern for
any workload, as it relates not just to application users but also mantainers. Threat
detection and identification is also an important concern for all workloads, especially
when operating in public environments (i.e. Cloud).



## Figurate Maturity Model

Whilst there are many concerns when operating modern software, it is also true
that it isn't realistic to be able to address all of them sufficiently on day one. The
Figurate Maturity Model identifies levels of maturity when addressing key concerns, such
that you may consider at a minimum what should be implemented based on the type of workload.

It is also true that some concerns may not even be supported by the target environment or
platform, but ultimately this model is a general guide provided to address concerns where
applicable.

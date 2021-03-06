# ClusterHealthz

[![Build Status](https://travis-ci.org/jbunce/clusterhealthz.svg?branch=master)](https://travis-ci.org/jbunce/clusterhealthz) [![Coverage Status](https://coveralls.io/repos/github/jbunce/clusterhealthz/badge.svg?branch=master)](https://coveralls.io/github/jbunce/clusterhealthz?branch=master)


## What is ClusterHealthz?

ClusterHealthz is a Python microservice that builds pre-determined Kubernetes
cluster health logic into a single reporting metric for an external load
balancer to monitor. The purpose of this is two fold:

- Firstly the ability to check whether a Kubernetes cluster hosting
services is in an operational state that is considered healthy. To do this,
metric based alert logic is required to be configured in Prometheus that will
raise an alarm in AlertManager if an event that is considered negative to
cluster operation occurs. This is not limited to the nodes that are carrying
production workloads; any configurable alarm event can be used to determine
cluster health.

- Secondly an individual workload node may fail and traffic should not be
directed there from an external load balancer.

## Why not use native load balancer monitors?

Load balancer monitoring is typically layer four or HTTP(S) based with checks
configured to verify a socket can be opened or an HTTP 200 OK received. With
a Kubernetes cluster there could be other considerations for allowing
production traffic to reach a cluster. This service allows you to define
Kubernetes specific statements such as: "Return healthy if the `etcd` nodes
fsync() is < 25ms over p99", or "Return unhealthy if the number of HTTP 500s
increases by x following a code push". Building that logic into a load balancer
monitor can become too complicated without the knowledge of events that are
impacting the cluster. The service can be sent a SIGHUP to reload the
configuration which reads and loads the alert data names.

This service requires Prometheus to be operational and will be return
'unhealthy' if the service cannot be reached.

## How can I configure the alerts?

If you want to configure your own alert conditions that mean that a Kubernetes
cluster should not be serving traffic, you can add your own custom alerts to
return an unhealthy status from the service. Add the alarm names you have
configured into `config/clusterhealthz.conf`

## How can I contribute?

PRs welcome. Please ensure your code is PEP8 compliant and tests are written
for new methods.

## Testing

Tests are configured in `clusterhealthz_test.py`
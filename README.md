<p align="center">
<a href="https://prow.build-infra.jetstack.net/?job=post-preflight-release-canary">
<!-- prow build badge, godoc, and go report card-->
<img alt="Build Status" src="https://prow.build-infra.jetstack.net/badge.svg?jobs=post-preflight-release-canary">
</a>
<a href="https://godoc.org/github.com/jetstack/preflight"><img src="https://godoc.org/github.com/jetstack/preflight?status.svg"></a>
<a href="https://goreportcard.com/report/github.com/jetstack/preflight"><img alt="Go Report Card" src="https://goreportcard.com/badge/github.com/jetstack/preflight" /></a>
</p>

# Jetstack Preflight

Preflight is a tool to automatically perform Kubernetes cluster configuration checks using [Open Policy Agent (OPA)](https://www.openpolicyagent.org/).

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Jetstack Preflight](#jetstack-preflight)
    - [Background](#background)
    - [Preflight Packages](#preflight-packages)
    - [Install Preflight](#install-preflight)
        - [Use Preflight locally](#use-preflight-locally)
        - [Preflight In-Cluster with periodic checks](#preflight-in-cluster-with-periodic-checks)

<!-- markdown-toc end -->


## Background

Preflight was originally designed to automate Jetstack's
production readiness assessments.
These are consulting sessions in which a Jetstack engineer inspects a customer's
cluster to suggest improvements and identify configuration issues. 
The product of this assessment is a report
which describes any problems and offers remediation advice.

While these assessments have provided a lot of value to many customers,
with a complex system like Kubernetes it's hard to thoroughly check everything.
Automating the checks allows them to be more comprehensive and much faster.

The automation also allows the checks to be run repeatedly,
meaning they can be deployed in-cluster to provide continuous configuration checking.

This enables new interesting use cases as policy compliance audits.

## Preflight Packages

Policies for cluster configuration are encoded into "Preflight Packages".

You can find some examples in [./preflight-packages](./preflight-packages) and you can also [write your own Preflight Packages](./docs/how_to_write_packages.md).

Preflight Packages are a very thin wrapper around OPA's policies. A package is made of [Rego](https://www.openpolicyagent.org/docs/latest/#rego) files (OPA's high-level declarative language) and a *Policy Manifest*.

The *Policy Manifest* is a YAML file intended to add metadata to the rules, so the tool can display useful information when a rule doesn't pass.

Since the logic in these packages is just Rego, you can add tests to your policies and use OPA's command line to run them (see [OPA Policy Testing tutorial](https://www.openpolicyagent.org/docs/latest/policy-testing/)).

Additionally, Preflight has a built-in linter for packages:

```
preflight package lint <path to package>
```

## Install Preflight

### Use Preflight locally

You can compile Preflight by running `make build`. It will create the binary in `builds/preflight`.

Create your `preflight.yaml` configuration file (you can take inspiration from the ones in `./examples`).

Run Preflight (by default it looks for `./preflight.yaml`)

```
preflight check
```

You can try `./examples/pods.preflight.yaml` without having to change a line, if you have your *kubeconfig* (~/.kube/config) pointing to a working cluster.

```
preflight check --config-file=./examples/pods.preflight.yaml
```

You will see a CLI formatted report if everything goes well. Also, you will get a JSON report in `./output`. 

If you want to visualice the report in your browser, you can access [preflight.jetstack.io](https://preflight.jetstack.io/) and load the JSON report. **This is a static website. Your report is not being uploaded to any server. Everything happens in your browser.**

You can give it a try without even running the tool, since we provide some report examples ([gke.json](./examples/reports/gke.json), [pods.json](./examples/reports/pods.json)) ready to be loaded in [preflight.jetstack.io](https://preflight.jetstack.io/).

### Preflight In-Cluster with periodic checks

See [Installation Manual: Preflight In-Cluster](./docs/installation_manual_in_cluster.md).

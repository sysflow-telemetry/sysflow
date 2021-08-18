---
layout: single
title:  "SysFlow ELK integration and tutorial"
date:   2021-08-20 13:55:48 +0200
tags:   update
author_profile: true
author: Andreas Schade
---

As of release [0.3.0-rc2](https://github.com/sysflow-telemetry/sf-processor/releases/tag/0.3.0-rc2),
sf-processor can write SysFlow telemetry records to Elastic! 

To illustrate this new feature, we have created a [tutorial](https://github.com/sysflow-telemetry/sf-deployments/tree/dev/integrations/elk) 
that showcases the integration of SysFlow with ELK. The setup is based entirely on Docker. It uses the SysFlow docker deployment
consisting of sf-collector and sf-processor containers, and utilizes [Anthony Lapenna's](https://github.com/deviantony)
[ELK Stack on Docker](https://github.com/deviantony/docker-elk/tree/tls) to deploy ElasticSearch
to store and query SysFlow data and Kibana for visualisation. A fifth container acts as an attacker and
executes a set of commands belonging to the [Discovery](https://attack.mitre.org/tactics/TA0007/)
and [Command and Control](https://attack.mitre.org/tactics/TA0011/) techniques of the
[MITRE ATT&CK&reg;](https://attack.mitre.org) framework.

![Architecture of the demonstrator](https://raw.githubusercontent.com/sysflow-telemetry/sf-deployments/dev/integrations/elk/images/pipeline.png?raw=true "Architecture of the demonstrator")

SysFlow collects the telemetry information created by the attack container. On this stream of raw events, SysFlow
applies a custom processing pipeline that stores SysFlow data in two Elastic indices. One part of the pipeline
uses rules for detecting the potential use of MITRE ATT&CK&reg; techniques, generates alerts for matching SysFlow
records, enriches them with the corresponding MITRE technique tags, and writes these alerts to an Elastic index.
The second part of the pipeline stores the raw SysFlow events in a separate Elastic index for in-depth
investigation of alerts. The pipeline converts SysFlow alerts and raw SysFlow events to [Elastic Common Schema](https://www.elastic.co/guide/en/ecs/current/index.html)
before indexing them into ElasticSearch. The contents of both indices can be visualized using Kibana.

![Kibana visualization of the alerts index](https://raw.githubusercontent.com/sysflow-telemetry/sf-deployments/dev/integrations/elk/images/alerts.png?raw=true "Kibana visualization of the alerts index")

Check this exciting new [integration](https://github.com/sysflow-telemetry/sf-deployments/tree/dev/integrations/elk)
out for instructions on how to run the code yourself, a description of the pipeline configuration, and a detailed
explanation of the results!





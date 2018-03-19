## Prometheus iDrac SNMP Exporter Dell Module

I've generated a Dell iDrac Module for use with the Prometheus SNMP Exporter. A good use case for this is monitoring your ESXi system health via an iDrac SNMP as vCenter plugins and golang binaries (for basic exporters) can't run on ESXi systems. I am going to assume most people already know how to use the prometheus snmp exporter below.

### How to use the Module

1. Copy the contents of snmp.yml in this repository into the snmp.yml file that the prometheus snmp exporter is using, or use the snmp.yml file as your configuration file for prometheus's snmp exporter if all you care about are Dell metrics.
2. Look below at how to configure your prometheus.yml file to use the `dell` module.


### Dell OID Status's
Most Dell OID's will provide a integer as a value, the status of those integers are usually, for most cases, the following:
More seen here: http://www.dell.com/support/manuals/us/en/19/dell-opnmang-srvr-admin-v8.2/snmp_idrac8-v4/idrac-mib?guid=guid-e686536d-bc8e-4e09-8e8b-de8eb052efee&lang=en-us
```
1. Other
2. Unknown
3. OK
4. Non-critical
5. Critical
6. Non-recoverable
```

### Prometheus.yml configuration

Add the below to your prometheus.yaml file, this will use the dell module that we added to the snmp exporter.

```
  - job_name: 'snmp'
    static_configs:
      - targets:
        - 'host.example.com'
    metrics_path: /snmp
    params:
      module: [dell_idrac]
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9116  # The SNMP exporter's real hostname:port.
```

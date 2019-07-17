---
title: Ship Zeek logs
logo:
  logofile: zeek.png
  orientation: vertical
data-source: Zeek
contributors:
  - malwaresmustdie
shipping-tags:
  - security
---

## Setup

###### Guided configuration

**You'll need**:
[Filebeat 7](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html),
root access

1.  Configure Zeek logging

    Configure your FortiGate firewall to send logs to your Filebeat server.
    Make sure you meet this configuration:

    * Log format: syslog
    * Send over: UDP
    * IP address: Filebeat server IP address
    * Port 514

    See the [FortiGate docs](https://docs.fortinet.com/product/fortigate/) for more information
    on configuring your FortiGate firewall.

    Sample commands for FortiOS 6.2
    {:.inline-header}

    ```
    config log syslogd setting
    set status enable
    set format default
    set server <FILEBEAT-SERVER-IP-ADDR>
    set port 514
    end
    ```

2.  Download the Logz.io certificate to your Filebeat server

    ```shell
    sudo wget https://raw.githubusercontent.com/logzio/public-certificates/master/COMODORSADomainValidationSecureServerCA.crt -P /etc/pki/tls/certs/
    ```

3.  Add UDP traffic as an input

    In the Filebeat configuration file (/etc/filebeat/filebeat.yml), add UDP to the filebeat.inputs section.

    {% include log-shipping/replace-vars.html token=true %}

    ```yaml
    # ...
    filebeat.inputs:
    - type: udp
      max_message_size: 10MiB
      host: "0.0.0.0:514"

      fields:
        logzio_codec: plain

        # Your Logz.io account token. You can find your token at
        #  https://app.logz.io/#/dashboard/settings/manage-accounts
        token: <<SHIPPING-TOKEN>>
        type: fortigate
      fields_under_root: true
      encoding: utf-8
      ignore_older: 3h
    ```

    If you're running Filebeat 7, paste this code block.
    Otherwise, you can leave it out.

    ```yaml
    # ... For Filebeat 7 only ...
    filebeat.registry.path: /var/lib/filebeat
    processors:
    - rename:
        fields:
        - from: "agent"
          to: "filebeat_agent"
        ignore_missing: true
    - rename:
        fields:
        - from: "log.file.path"
          to: "source"
        ignore_missing: true
    ```

    If you're running Filebeat 6, paste this code block.

    ```yaml
    # ... For Filebeat 6 only ...
    registry_file: /var/lib/filebeat/registry
    ```

4.  Add Logz.io as an output

    If Logz.io is not an output, add it now.

    {% include log-shipping/replace-vars.html listener=true %}

    ```yaml
    # ...
    output.logstash:
      hosts: ["<<LISTENER-HOST>>:5015"]
      ssl:
        certificate_authorities: ['/etc/pki/tls/certs/COMODORSADomainValidationSecureServerCA.crt']
    ```

5.  Start Filebeat

    Start or restart Filebeat for the changes to take effect.

6.  Check Logz.io for your logs

    Give your logs some time to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

    If you still don't see your logs, see [log shipping troubleshooting]({{site.baseurl}}/user-guide/log-shipping/log-shipping-troubleshooting.html).
{:.tasklist.firstline-headline}

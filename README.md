# Fluentd Installed Ubuntu

## Prepare the config

```
<source>
  type tail
  format apache
  path /logs/access.log
  tag nginx.access
#  pos_file /logs/nginx.pos
</source>

<match nginx.access>
  type bigquery

  method insert    # default

  auth_method private_key   # default
  email 2881...pn1h@developer.gserviceaccount.com # replace yours
  private_key_path /data/mykey.p12 # replace yours
  # private_key_passphrase notasecret # default

  project my-project-id # replace yours
  dataset bq_test # replace yours
  auto_create_table true
  table   access_log

  time_format %s
  time_field  time

  field_integer time,status,bytes
  field_string  rhost,vhost,path,method,protocol,agent,referer
  field_float   requesttime
  field_boolean bot_access,loginsession
</match>
```

Replace the following items:
* email: the service account mail that apply from google console
* private_key_path: the p12 key file full path that apply from google console
* project: the google cloud project that you use
* dataset: the BigQuery dataset you want to import data
* table: the BigQuery table you want to import data

## Start the fluentd instance

```
docker run -v ~/data:/data -v ~/logs:/logs \
  -v /home/simonsu/dockerws/fluentd/td-agent:/etc/td-agent \
  -it peihsinsu/fluentd bash
```

* /data: I put the p12 key here
* /etc/td-agent: Where the td-agent.conf exist
* /logs: Where the log file exist

## Start the daemon inside instance

```
# /etc/init.d/td-agent start
```

## Watch the log

```
# tail -f /var/log/td-agent/td-agent.log
```

## Other references

* BigQuery plugin for td-agent configure reference: https://github.com/kaizenplatform/fluent-plugin-bigquery#configuration
* Google fluentd sample: https://github.com/GoogleCloudPlatform/bigquery-fluentd-docker-sample
* Kaz's slide: https://speakerdeck.com/googlecloudjapan/mobairu-kpi-fen-xi-falsexin-biao-zhun-fluentd-plus-google-bigquery-number-gcpraibu-number-gcpja


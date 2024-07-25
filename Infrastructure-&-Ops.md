## Production Infrastructure

* Single server hosted on Azure sponsored hosting with a staging server on Linode.
* Rails
* PostgresDB
* Puma
* Instance is used for all Diaper Banks (hosted service, not federated or self-hosted)

## Production Activity

* There are no particular times of usage, though likely US-daylight

## Deployment

* Cloud66 deploys from production branch
  * See [README.md#Deployment-Process](https://github.com/rubyforgood/human-essentials?tab=readme-ov-file#deployment-process)
  * See [.cloud66/manifest.yml](https://github.com/rubyforgood/human-essentials/blob/main/.cloud66/manifest.yml)
  * See [config/puma.rb](https://github.com/rubyforgood/human-essentials/blob/main/config/puma.rb)
  * See [config/environments/production.rb](https://github.com/rubyforgood/human-essentials/blob/main/config/environments/production.rb) for production config
  * Single server for DB and web server (Puma)
  * Uses hard-coded ip address

## Monitoring, Alerting, Metrics

* Bugsnag (we have an OSS account, contact an admin for access to it)
* Skylight - Monitoring service

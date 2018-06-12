## Production Infrastructure

* Single server hosted on (?)
* PostgresDB
* Puma
* Instance is used for all Diaper Banks (hosted service, not federated or self-hosted)

## Production Activity

* Right now we are in beta-testing with 5 (?) diaper banks
* These diaper banks are (?) trying to use the system with real-world data
* That means the system is IN PRODUCTION and is the source-of-truth for some users
* There are no particular times of usage, though likely US-daylight
* Q: How do we communicate with users, like if we needed downtime?
  * Testing users are encouraged to submit github issues (all of them? trained?)

## Deployment

* TravisCI deploys master
  * Triggered by merge into master
  * Runs capistrano deploy if the rest of the tests pass
  * See [.travis.yml](https://github.com/rubyforgood/diaper/blob/master/.travis.yml)
* Capistrano is used to perform the deploy
  * See [config/deploy.rb](https://github.com/rubyforgood/diaper/blob/master/config/deploy.rb) for general config
  * See [config/deploy/production.rb](https://github.com/rubyforgood/diaper/blob/master/config/deploy/production.rb) for production config
  * Single server for DB and web server (Puma)
  * Uses hard-coded ip address

## Monitoring, Alerting, Metrics

* Bugsnag (?)
* [Skylight](https://oss.skylight.io/app/applications/LrXHcxDK7Be9/recent/6h/endpoints) - Monitoring service

## Production Debugging

* A few admins (a-a-ron, sean, who else (?)) have production shell access

## Disaster Recovery

* [Issue #405 will add DB backups](https://github.com/rubyforgood/diaper/issues/405)
* VM Snapshot (?)

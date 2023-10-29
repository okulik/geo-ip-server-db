# Geo IP server database

This repo contains HA PostgreSQL instance suitable for deploying to [fly.io](https://fly.io) platform. It was forked from [fly-apps/postgres-ha](https://github.com/fly-apps/postgres-ha)* only to include ip4r extension, as the original repo had installed only postgis and timescaledb. Check this[README.md](https://github.com/fly-apps/postgres-ha#readme) for details.

This repo accompanies [Geo IP server](https://github.com/okulik/geo-ip-server) repo containing the API server code. Check the linke for firther details.

* There's a [newer way]([https://github.com/fly-apps/postgres-flex) how to deploy HA POsthgreSQL instance to fly.io. It doesn't use [stolon](https://github.com/sorintlab/stolon) and [consul](https://www.consul.io) but [repmgr](http://www.repmgr.org) - worth checking.

## Deploying PostgreSQL to fly.io

To create a new fly.io application running PostgreSQL, run this first:
```bash
$ fly launch --no-deploy
```
 Choose yes when asked whether to copy the existing configuration to the newly generated app and change the application name to your liking.

Following that, run `fly secrets` to set required secrets.
 ```bash
 $ fly secrets set SU_PASSWORD=<PASSWORD> REPL_PASSWORD=<PASSWORD> OPERATOR_PASSWORD=<PASSWORD>
 ```
 Remember to replace each PASSWORD with a proper, unique secret. Also, change `PRIMARY_REGION` inside fly.toml to your preferred region (currently set to Amsterdam).

 Create a volume of appropriate size, within the same region as the app (replace ams with your region):
 ```bash
 $ fly volumes create pg_data --region ams --size 10 # create 10 Gib volume for storage
```
Answer with 'y' to the `Do you still want to use the volumes feature?` question.

And finally, deploy the PostgreSQL server:
```bash
 $ fly deploy
 ```

 If the database won't start and logs contain `panic: FLY_CONSUL_URL or CONSUL_URL are required`, make sure to run the following command:
 ```bash
 $ fly consul attach
 ```

 For any further issues, consult the [repos's documentation](https://github.com/fly-apps/postgres-ha).

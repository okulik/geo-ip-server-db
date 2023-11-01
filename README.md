# Geo IP server database

This repo contains HA PostgreSQL instance suitable for deploying to [fly.io](https://fly.io) platform. It was forked from [fly-apps/postgres-ha](https://github.com/fly-apps/postgres-ha)[1] only to remove all unnecessary extensions. Check this[README.md](https://github.com/fly-apps/postgres-ha#readme) for details.

This repo accompanies [Geo IP server](https://github.com/okulik/geo-ip-server) repo containing the API server code. Check the link for further details.

[1] There's a [newer way]([https://github.com/fly-apps/postgres-flex) how to deploy HA POsthgreSQL instance to fly.io which doesn't use [stolon](https://github.com/sorintlab/stolon) and [consul](https://www.consul.io) but [repmgr](http://www.repmgr.org).

## Deploying PostgreSQL to fly.io

To create a new fly.io application running PostgreSQL, run this first:
```bash
fly launch --no-deploy
```
Choose yes when asked whether to copy the existing configuration to the newly generated app and change the application name to your liking.

Following that, run `fly secrets` to set required secrets.
```bash
fly secrets set SU_PASSWORD=<password1> REPL_PASSWORD=<password2> OPERATOR_PASSWORD=<password3>
```
Remember to replace each PASSWORD with a proper, unique secret. Also, change `PRIMARY_REGION` inside fly.toml to your preferred region (currently set to Amsterdam).

Create a volume of appropriate size (5 GiB should be enough), within the same region as the app (replace ams with your region):
```bash
fly volumes create pg_data --region ams --size 5
```
Answer with 'y' to the `Do you still want to use the volumes feature?` question.


And finally, deploy the PostgreSQL server:
```bash
fly deploy
```

If the database starts but the health check keeps returning errors like  `panic: FLY_CONSUL_URL or CONSUL_URL are required`, make sure to run the following command:
```bash
fly consul attach
```

Almost done. To make things work with MaxMind Geolite2 City data, we need to increase instance's memory from 256 to 512 MiB.
```bash
fly scale memory 512
```

For any further issues, consult the [forked repos's](https://github.com/fly-apps/postgres-ha) documentation.

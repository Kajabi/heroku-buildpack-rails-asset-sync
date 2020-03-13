# Rails Asset Sync Heroku Buildpack

This buildpack syncs the contents of an app's `public` directory with a storage target like S3, where you might have a CDN like Fastly pointed. It is intended to be run during staging builds on Heroku as part of a deployment pipeline.

Its goals are:

- Simplify slug promotion between staging and production. Staging builds the slug, where assets have baked in a CDN URL that staging and production can share.
- Reduce the size of the built slug by removing uncompiled assets from `app/assets` and compiled assets from `public`.

## Configuration

The buildpack uses `rclone` to do the sync operation. If available, the buildpack will source `heroku/asset-sync-setup.sh`, where you can [configure rclone using environment variables](https://rclone.org/docs/#environment-variables). The rclone remote used by this buildpack is called `asset_sync`. For example:

```sh
# heroku/asset-sync-setup.sh

export RCLONE_LOG_LEVEL=ERROR
export RCLONE_CONFIG_ASSET_SYNC_TYPE=s3
export RCLONE_CONFIG_ASSET_SYNC_PROVIDER=AWS
export RCLONE_CONFIG_ASSET_SYNC_REGION=us-east-1
export RCLONE_CONFIG_ASSET_SYNC_ACCESS_KEY_ID=$(cat $ENV_DIR/AWS_ACCESS_KEY)
export RCLONE_CONFIG_ASSET_SYNC_SECRET_ACCESS_KEY=$(cat $ENV_DIR/AWS_SECRET_KEY)
```

## HBD

Use with caution. Heroku [does not advise using the asset-sync gem](https://devcenter.heroku.com/articles/please-do-not-use-asset-sync), which this buildpack essentially replicates (albeit greatly simplified). We weighed the downsides outlined by Heroku and felt our use case was appropriate, but keeping assets as part of your app's slug is more deterministic and therefore much simpler to deploy with confidence.

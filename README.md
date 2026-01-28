# Remote Settings Ingestion Cronjob - `ai-window-prompts` Collection

This repo contains code to update the [ai-window-remote-settings-prompts](https://github.com/Firefox-AI/ai-window-remote-settings-prompts) collection in PROD Remote-Settings. It is intended to be run as a scheduled job (cronjob).

## Run

With local Remote Settings server:

```
$ docker run --rm --detach \
    -p 8888:8888 \
    --env KINTO_INI=config/testing.ini \
    mozilla/remote-settings
```

Create the source collection:

```
$ curl -X PUT http://localhost:8888/v1/buckets/main-workspace/collections/ai-window-prompts
```

And run the script. 

**Note:** Provided `$GIT_TOKEN` must have access to [Firefox-AI/ai-window-remote-settings-prompts](https://github.com/Firefox-AI/ai-window-remote-settings-prompts)

```
$ uv sync
$ SERVER="http://localhost:8888/v1" GIT_TOKEN=$GIT_TOKEN uv run script.py

Fetch server info...✅
⚠️ Anonymous
Fetch records from source of truth...✅
Fetch current destination records...✅
Batch #0: PUT /buckets/main-workspace/collections/product-integrity/records/release - 201
Batch #1: PUT /buckets/main-workspace/collections/product-integrity/records/beta - 201
Batch #2: PUT /buckets/main-workspace/collections/product-integrity/records/esr - 201
Apply changes...3 operations ✅
Request review...✅
```

### On Remote Settings official servers

([List of environment servers](https://remote-settings.readthedocs.io/en/latest/getting-started.html#environments))

**As yourself**:

Login on the Admin UI and copy the Bearer header value (UI top right bar)

And use it to run the script

```
$ read -s BEARER
$ AUTHORIZATION=$BEARER SERVER="http://remote-settings.mozilla.org/v1" GIT_TOKEN=$GIT_TOKEN python script.py
```

**Using an account**:

```
$ read -s PASSWD
$ AUTHORIZATION=fxrelay-publisher:$PASSWD ENVIRONMENT=prod GIT_TOKEN=$GIT_TOKEN python script.py
```

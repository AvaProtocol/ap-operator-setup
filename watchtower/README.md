# AP Operator Auto Update

To perform auto update, we use
[watchtower](https://github.com/containrrr/watchtower). It
watches `ap_operator` container and check for the docker image update.
Once a new image is pushed to docker hub, `watchtower` pulls the new image,
then restarts the container that it momonitors with the new docker image.

Obviously this kind of update will only work when there is no config change
required. If there is a configuration change that requires you to set a flag in the
config, then we will need to perform a manually config change and then update.

However, we try hard to make the update painless and majority of time we can let
it auto updated.

To run the auto update simply perform:


```
# make sure you're inside the `watchtower` directory.
# then bring up docker compose
docker compose up -d

```

you should see something like this


```
 ✔ Container watchtower  Started
```

To check the log you can do:


```
docker compose logs -f
```

And it should show up like this:


```
❯ docker compose logs -f
watchtower  | time="2024-07-03T00:01:58Z" level=info msg="Watchtower 1.7.1"
watchtower  | time="2024-07-03T00:01:58Z" level=info msg="Using no notifications"
watchtower  | time="2024-07-03T00:01:58Z" level=info msg="Only checking containers which name matches \"ap_operator\""
watchtower  | time="2024-07-03T00:01:58Z" level=info msg="Scheduling first run: 2024-07-03 08:01:58 +0000 UTC"
watchtower  | time="2024-07-03T00:01:58Z" level=info msg="Note that the first check will be performed in 7 hours, 59 minutes, 59 seconds"
```

You can read more about the
[watchtower](https://github.com/containrrr/watchtower) with advanced feature
such as Slack notification and build your own docker compose based on our file
here.

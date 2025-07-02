## What is this?

This is an example `fly.toml` to run Home Assistant Container on fly.io.

## Installation

1. Clone the repository.
2. In the repository directory, run `flyctl launch`.
3. Wait for the app to be deployed. Once done, ssh to the app using `fly ssh console -s`. First install a text editor:

```bash
apk update && apk add micro
```

Then use it to edit `configuration.yaml` to append the following to the end:

```yaml
# This configures Home Assistant to accept connections from the Fly's reverse proxy.
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 172.16.0.0/12
```

4. Close the connection.
5. In the repository directory, restart the app with `fly apps restart`.
6. Now you can create a user and log in to Home Assistant at your app URL.

To redeploy after making changes to `fly.toml`, execute `fly deploy`.

## FAQ

### What is the cost?

At the server size specified in the file it costs $8/month (calculated with fly.io calculator at the time of writing).

If you enable autostop as specified in the comments it can be much lower, less than $1/month if you use HA infrequently, but this might break most Home Assistant setups as you won't have an always-on HA server.

### How to actually connect this to my home network?

This part is left as an exercise to the reader. You might consider setting up a Raspberry Pi as a tailscale subnet router at your home. This is not implemented here. If you figure out a successful setup, feel free to open a PR.

### This solved my problem, how can I thank you?

You can drop me a tip easily from [here](https://github.com/sponsors/rayanamal)

### Why do we override the entrypoint and CMD of the original Home Assistant container?

Home assistant uses S6 as its init system. S6 expects to run as PID 1. This is afaict not possible with fly.io, except through the experimental *"Pilot"* progam which you need to contact support to manually enroll in.

After much investigation, I found that S6 as it's used in the Home Assistant container does nothing more or less than executing the ENTRYPOINT and CMD commands that is specified in the `fly.toml`. Thus it's bypassed altogether.

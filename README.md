# oauth2_proxy on Docker

This repository holds a build definition and supporting files for building a Docker image to run [oauth2_proxy](https://github.com/bitly/oauth2_proxy).
It is published as automated build `machine-data/oauth2_proxy` on [Docker Hub](https://registry.hub.docker.com/u/machinedata/oauth2_proxy/).

## What is oauth2_proxy?

[oauth2_proxy](https://github.com/bitly/oauth2_proxy) is a reverse proxy and static file server that provides authentication using Providers (Google, GitHub, and others) to validate accounts by email, domain or group.

## Yet another oauth2_proxy container?

Not quite:
- Based on the official Alpine Linux image - super slim and lightweight.
- No magic. Straight config that follows upstream. Simple and clean configuration via environment variables _or_ config file.
- Image follows [Dockerfile best practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/) (dropping root privileges, PID1 for proper signalling, logging,...)

## Quickstart

To be able to start oauth2_proxy you need to configure an OAuth Provider first. Instructions for Google and others are found on the [oauth2_proxy website](https://github.com/bitly/oauth2_proxy#google-auth-provider). Note your `client-id` and `client-secret`.

In the minimal configuration you also need to specify the `upstream` you are protecting.


```sh
$ docker run -d -p 4180:4180 \
    -e OAUTH2_PROXY_CLIENT_ID=... \
    -e OAUTH2_PROXY_CLIENT_SECRET=... \
    -e OAUTH2_PROXY_UPSTREAM=... \
    machine-data/oauth2_proxy
```

## Environment variables

It is very easy to configure oauth2_proxy via environment variables. If no config file is present, the `docker-entrypoint.sh` script will create one based on the passed environment variables.

- `OAUTH2_PROXY_CLIENT_ID`: the OAuth Client ID: ie: "123456.apps.googleusercontent.com"

- `OAUTH2_PROXY_CLIENT_SECRET`: the OAuth Client Secret

- `OAUTH2_PROXY_COOKIE_SECRET`: the seed string for secure cookies. To generate a strong cookie secret just run `python -c 'import os,base64; print base64.b64encode(os.urandom(16))'`.

- `OAUTH2_PROXY_EMAIL_DOMAIN`: authenticate emails with the specified domain (may be given multiple times). The default is "*" and will authenticate any email.

- `OAUTH2_PROXY_UPSTREAM`: the http url(s) of the upstream endpoint or file:// paths for static files. Routing is based on the path


You can pass any variable that is specified on the [command line options](https://github.com/bitly/oauth2_proxy#command-line-options) documentation.

- `OAUTH2_PROXY_APPROVAL_PROMPT`
- `OAUTH2_PROXY_AUTHENTICATED_EMAILS_FILE`
- `OAUTH2_PROXY_AZURE_TENANT`
- `OAUTH2_PROXY_BASIC_AUTH_PASSWORD`
- `OAUTH2_PROXY_CONFIG`
- `OAUTH2_PROXY_COOKIE_DOMAIN`
- `OAUTH2_PROXY_COOKIE_EXPIRE`
- `OAUTH2_PROXY_COOKIE_HTTPONLY`
- `OAUTH2_PROXY_COOKIE_NAME`
- `OAUTH2_PROXY_COOKIE_REFRESH`
- `OAUTH2_PROXY_COOKIE_SECURE`
- `OAUTH2_PROXY_CUSTOM_TEMPLATES_DIR`
- `OAUTH2_PROXY_DISPLAY_HTPASSWD_FORM`
- `OAUTH2_PROXY_GITHUB_ORG`
- `OAUTH2_PROXY_GITHUB_TEAM`
- `OAUTH2_PROXY_GOOGLE_ADMIN_EMAIL`
- `OAUTH2_PROXY_GOOGLE_GROUP`
- `OAUTH2_PROXY_GOOGLE_SERVICE_ACCOUNT_JSON`
- `OAUTH2_PROXY_HTPASSWD_FILE`
- `OAUTH2_PROXY_HTTP_ADDRESS`
- `OAUTH2_PROXY_HTTPS_ADDRESS`
- `OAUTH2_PROXY_LOGIN_URL`
- `OAUTH2_PROXY_PASS_ACCESS_TOKEN`
- `OAUTH2_PROXY_PASS_BASIC_AUTH`
- `OAUTH2_PROXY_PASS_HOST_HEADER`
- `OAUTH2_PROXY_PROFILE_URL`
- `OAUTH2_PROXY_PROVIDER`
- `OAUTH2_PROXY_PROXY_PREFIX`
- `OAUTH2_PROXY_REDEEM_URL`
- `OAUTH2_PROXY_REDIRECT_URL`
- `OAUTH2_PROXY_RESOURCE`
- `OAUTH2_PROXY_REQUEST_LOGGING`
- `OAUTH2_PROXY_SCOPE`
- `OAUTH2_PROXY_SIGNATURE_KEY`
- `OAUTH2_PROXY_SKIP_AUTH_REGEX`
- `OAUTH2_PROXY_SKIP_PROVIDER_BUTTON`
- `OAUTH2_PROXY_TLS_CERT`
- `OAUTH2_PROXY_TLS_KEY`
- `OAUTH2_PROXY_VALIDATE_URL`

## Configuration file

The container is configured to start oauth2_proxy with `/config/oauth2_proxy.cfg` as config file.
If a config file is mounted (preferably read-only), the `OAUTH2_PROXY_` environment variables will be ignored. Use the [example config](https://github.com/bitly/oauth2_proxy/blob/master/contrib/oauth2_proxy.cfg.example) to start:

```sh
$ curl -O https://raw.githubusercontent.com/bitly/oauth2_proxy/master/contrib/oauth2_proxy.cfg.example
$ mv oauth2_proxy.cfg.example oauth2_proxy.cfg
$ sed -i -e "s/# http_address = .*/http_address = \"0.0.0.0:4180\"/" oauth2_proxy.cfg.example
$ docker run -d \
             -v $(pwd)/oauth2_proxy.cfg.example:/config/oauth2_proxy.cfg:ro \
             -p 4180:4180 machine-data/oauth2_proxy
```

## Volumes

- `/templates`: Path to place custom templates `sign_in.html` and `error.html`. You also need to set `custom-templates-dir` via config file or the `OAUTH2_PROXY_CUSTOM_TEMPLATES_DIR` environment variable.

## Ports

- `4180`: The default port where oauth2_proxy is listening. Can be changed via `http-address` (and/or `https_address`) setting and corresponding `OAUTH2_PROXY_` environment variable.

## Legal

oauth2_proxy is a creation of [bitly](http://word.bitly.com/post/47548678256/google-auth-proxy) and was renamed from Google Auth Proxy in May 2015.
It is licensed under the [MIT license](https://github.com/bitly/oauth2_proxy/blob/master/LICENSE).

docker-oauth2_proxy is licensed under the [Apache 2.0 license](https://github.com/machine-data/docker-oauth2_proxy/blob/master/LICENSE), was created by [Jodok Batlogg](https://github.com/jodok).
Copyright 2016 [Crate.io, Inc.](https://crate.io).

## Contributing

Thanks for considering contributing to docker-oauth2_proxy!
The easiest way to contribute is either by filing an [issue on Github](https://github.com/machine-data/docker-oauth2_proxy/issues) or to [fork the repository](https://github.com/machine-data/docker-oauth2_proxy/fork) to create a pull request.

If you have any questions don't hesitate to join us on Slack.

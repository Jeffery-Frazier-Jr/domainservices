# Local Navidrome + Authelia Setup

This repo contains configuration files to run Navidrome behind Authelia using nginx.

## Certificates

`mbuntuattempt2/certs` includes a self signed CA (`ca.crt`, `ca.key`) and a server certificate (`server.crt`, `server.key`) generated from that CA. The server certificate is issued for `localhost` and is used by nginx.

## Services

- **Authelia** is configured via `mbuntuattempt2/authelia/config.yml` and stores user data in `mbuntuattempt2/authelia/users_database.yml`.
- **Navidrome** runs with `EnableAuthentication` disabled using `mbuntuattempt2/navidrome/navidrome.toml`.
- **nginx** acts as TLS terminator and reverse proxy. Configuration lives under `mbuntuattempt2/nginx/`.

## Running

Start each component in separate terminals:

```bash
# Authelia
cd mbuntuattempt2/authelia
./authelia --config config.yml
```

If the binary isn't executable run `chmod +x ./authelia` first.

```bash
# Navidrome
cd mbuntuattempt2/navidrome
./navidrome --configfile navidrome.toml
```

Run `chmod +x ./navidrome` if it cannot be executed.

```bash
# nginx (run from repo root)
nginx -p $(pwd)/mbuntuattempt2/nginx -c nginx.conf
or
nginx -p $(pwd)/mbuntuattempt2/nginx -c nginx.conf -t
```

Logs written by nginx are available under `mbuntuattempt2/logs`.

`navidrome.conf` includes the file `authelia-forward.inc` which defines the
forward-auth locations used to check authentication with Authelia. Authelia
is now served over HTTPS using the certificates in `mbuntuattempt2/certs` so
nginx forwards requests to `https://localhost:9091`.

Then access `https://music.localhost.localdomain` in a browser that trusts `mbuntuattempt2/certs/ca.crt`.

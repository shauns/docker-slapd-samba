## slapd, with Samba schema

Based on, and many thanks to, https://github.com/nickstenning/docker-slapd

A basic configuration of the OpenLDAP server, slapd, with support for data
volumes, and Samba schema applied.

This image will initialize a basic configuration of slapd. Most common schemas
are preloaded (all the schemas that come preloaded with the default Ubuntu
Precise install of slapd), as well as the aforementioned Samba schema, but the 
only record added to the directory will be the root organisational unit.

You can (and should) configure the following by providing environment variables
to `docker run`:

- `LDAP_DOMAIN` sets the LDAP root domain. (e.g. if you provide `foo.bar.com`
  here, the root of your directory will be `dc=foo,dc=bar,dc=com`)
- `LDAP_ORGANISATION` sets the human-readable name for your organisation (e.g.
  `Acme Widgets Inc.`)
- `LDAP_ROOTPASS` sets the LDAP admin user password (i.e. the password for
  `cn=admin,dc=example,dc=com` if your domain was `example.com`)

For example, to start a container running slapd for the `mycorp.com` domain,
with data stored in `/data/ldap` on the host, use the following:

```bash
    docker run -p 389:389
               -v /data/ldap:/var/lib/ldap \
               -e LDAP_DOMAIN=mycorp.com \
               -e LDAP_ORGANISATION="My Mega Corporation" \
               -e LDAP_ROOTPASS=s3cr3tpassw0rd \
               -d shauns/docker-slapd-samba
```

The example above will expose LDAP over the standard port 389 (through the 
`-p` option), but if you chose to omit it you can find out which port the 
LDAP server is bound to on the host by running `docker ps` (or 
`docker port <container_id> 389`). You could then load an LDIF
file (to set up your directory) like so:

```bash
ldapadd -h localhost -p <host_port> -c -x -D cn=admin,dc=mycorp,dc=com -W -f data.ldif
```

**NB**: Please be aware that by default docker will make the LDAP port
accessible from anywhere if the host firewall is unconfigured.

# SSO

Attempt at an SSO system with WSO2 IS and 389DS (feat. Vagrant & Ansible)

**This repo is not maintained** since we ended up using Keycloak instead of
WSO2 IS in production. However, it may still be useful to people
that want to set up WSO2 IS with an external LDAP server.

The production Keycloak configuration will be made public in the future,
possibly along a development/test environment managed by Vagrant to
replace this one.

In the meantime, you can take a look at some other maintained repos:

* [389DS server Ansible role](https://github.com/lvps/389ds-server) (used in production)
* [389DS replication Ansible role](https://github.com/lvps/389ds-replication)
* [389DS examples](https://github.com/lvps/389ds-examples/)
* [Schema files and ACIs for 389DS](https://github.com/weee-open/schema) (used in production)

## What

The Vagrant configuration will bring up two 389DS servers with master-master
replication, 1 WSO2 IS server, 1 web server (PHP, nginx, MariaDB) for
Nextcloud (Nextcloud setup is not automated, however).

## How

Some Ansible roles need to be downloaded.

```shell
ansible-galaxy install -p roles bertvv.mariadb
cd roles
git clone https://github.com/lvps/389ds-server.git
git clone https://github.com/lvps/389ds-replication.git
cd ..
```

Then, a few custom schema files that go into the `schema` directory (created
by git):

```shell
git clone https://github.com/WEEE-Open/schema
```

Finally, you'll need some self-signed certificates...

```shell
cd ca
./cert.sh ldap1.sso.local
./cert.sh ldap2.sso.local
cd ..
```

Now do `vagrant up` and wait for it to bring up 3 machines:

| Vagrant name | Hostname           | IP         |
|--------------|--------------------|------------|
| mm1          | ldap1.sso.local    | 10.55.0.10 |
| mm2          | ldap2.sso.local    | 10.55.0.20 |
| wso2         | wso2.sso.local     | 10.55.0.30 |

There's an `hosts` file that get deployed to the VMs, but you can use it as
an example to edit your hosts file and reach the 389DS servers and WSO2 IS
with their hostname instead of an IP.

The 389DS setup is almost the same as in the [Multi-master with 2 masters](https://github.com/lvps/389ds-examples/#multi-master-with-2-masters)
example from another one of our repos, so follow these instructions to start
replication manually. More precisely, `nsds5ReplicaEnabled` is already `on`
but you will need to perform a total reinit with `nsds5BeginReplicaRefresh: start`
on mm1, just follow the readme. It also contains some details on how the servers
and TLS are configured.

### WSO2 IS configuration files

Some files have been changed from thei default:

* application-authentication.xml: configure `<AuthenticatorConfig name="totp" enabled="true">` section
* carbon.xml: set `<HostName>` and `<MgtHostName>`, disable `<HideAdminServiceWSDLs>`
* catalina-server.xml: change enabled protocols and keystore location & password
* embedded-ldap.xml: disable it entirely
* identity.xml: this `<Resource context="(.*)/.well-known(.*)" secured="false" http-method="all"/>` and disable consent management
* user-mgt.xml: configure external LDAP user store

When a new version is released, rather than keeping the old configuration files, it's better to grab fresh ones from the updated server and apply changes again. This may break some stuff, but it's probably better than being the only person on the planet to still have *that* option set to false from 10 releases ago and it causes random errors that nobody else on the Internet has ever mentioned...

## License

MIT unless otherwise noted (some of the involved software and Ansible roles have a different license)

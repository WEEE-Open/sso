# SSO

Attempt to build a SSO system with Keycloak, 389DS and MariaDB (feat. Vagrant & Ansible)

**Heavily work in progress.**

## What

Two 389DS servers with master-master replication, 1 Keycloak server also running
MariaDB for its internal database.

The goal is to have a fully replicated setup: a Galera cluster for MariaDB
replication and somehow getting Infinispan to work without a LAN for Keycloak.
And maybe adding some HAProxy on top of it all.

## How

Some Ansible roles need to be downloaded.

```shell
mkdir roles
ansible-galaxy install -p roles bertvv.mariadb
cd roles
git clone https://github.com/lvps/389ds-server.git
git clone https://github.com/lvps/389ds-replication.git
git clone https://github.com/lvps/ansible-wildfly-mariadb-connector-j.git
# Until that pull request is merged...
git clone https://github.com/jdennis/ansible-keycloak
mv ansible-keycloak nkinder.keycloak
cd nkinder.keycloak && git checkout -B oooq-initial origin/oooq-initial && cd ..
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
./cert.sh ldap1.example.local
./cert.sh ldap2.example.local
```

Now do `vagrant up` and wait for it to bring up 3 machines:

| Vagrant name | Hostname               | IP          |
|--------------|------------------------|-------------|
| mm1          | ldap1.example.local    | 10.38.9.10  |
| mm2          | ldap2.example.local    | 10.38.9.20  |
| keycloak     | keycloak.example.local | 10.38.9.199 |

There's an `hosts` file that get deployed to the VMs, but you can use it as
an example to edit your hosts file and reach the 389DS servers and Keycloak
with their hostname instead of an IP.

The 389DS setup is almost the same as in the [Multi-master with 2 masters](https://github.com/lvps/389ds-examples/#multi-master-with-2-masters)
example from another one of our repos, so follow these instructions to start
replication manually. More precisely, `nsds5ReplicaEnabled` is already `on`
but you will need to perform a total reinit with `nsds5BeginReplicaRefresh: start`,
just follow the readme. It also contains some details on how the servers and TLS
are configured.

The Keycloak part right now is a mess and I'm changing half of it every other
day, so there's no point in describing it right now, just look at keycloak.yml.

## License

MIT unless otherwise noted (some of the involved software and Ansible roles have a different license)

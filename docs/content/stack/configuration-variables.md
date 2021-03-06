---
title: "Configuration Variables"
weight: 5
aliases:
  - /en/master/advanced/stack-config/
---
## Configuration Variables {#configuration-variables}

Configuration variables can be specified in the `docksal.env` file or by running the `fin config set` command (e.g., `fin config set --global PROJECT_INACTIVITY_TIMEOUT="0.5h"`). All variables that are changed from their defaults below will require, at minimum, a project restart `fin restart` so that they can take effect. Additional steps may be required.

### DOCKSAL_NFS_PATH

The location of the folder on the host machine to mount to VirtualBox. See [file sharing](/core/file-sharing/) for more information.

### IMAGE_SSH_AGENT

`Default: docksal/ssh-agent:1.0`

The Docker image to use for SSH Agent. This contains the keys that are shared among the projects.

### IMAGE_VHOST_PROXY 

`Default: docksal/vhost-proxy:1.3` 

Docker image to use as the VHOST Proxy. This forwards all requests to the appropriate container based upon the hostname that is being requested.

### IMAGE_DNS

`Default: docksal/dns:1.0`

Docker image to use for DNS Routing.

### CI

This is a global variable. Only use on Linux servers. 

When set, enables CI mode:

- Sets `DOCKSAL_VHOST_PROXY_IP="0.0.0.0"`, which opens vhost-proxy to the world.
- Reports the instance as a CI instance (vs local), so our anonymous usage stats are not skewed.
- Sets `DOCKSAL_SSH_AGENT_USE_HOST=1`, which enables reuse of the hosts `SSH_AUTH_SOCK` socket (Linux only).

This should be used on sandbox servers (and in CI) at the time of Docksal installation like this:

```
curl -fsSL https://get.docksal.io | CI=1 bash
```

### DOCKSAL_SSH_AGENT_USE_HOST

Defaults to `0` for non-CI environments and to `1` for CI environments.

When set to `1`, project's stack will prefer the host's SSH agent (`SSH_AUTH_SOCK` socket) over the built-in docksal-ssh-agent.
Can be used in conjunction with [SSH agent forwarding](https://developer.github.com/v3/guides/using-ssh-agent-forwarding/).

Only applicable to Linux hosts.

### DOCKSAL_LOCK_UPDATES 

`Default: 0`

When set to `1`, this will prevent Docksal from installing and checking for updates. This is usually good in combination with `CI=true`.

### DOCKER_VERSION_ALERT_SUPPRESS

When set, suppresses alert about the outdated Docksal version.

### DOCKSAL_ENVIRONMENT 

`Default: local`

Allow for environment specific YML and ENV files. `fin` will load additional configuration from 
`docksal-${DOCKSAL_ENVIRONMENT}.yml` and `docksal-${DOCKSAL_ENVIRONMENT}.env`. 

Default usage is creating `docksal-local.yml` and/or `docksal-local.env` for local overrides that are not intended 
to be committed.

In CI, you might set the value via `export DOCKSAL_ENVIRONMENT=ci` in your build script or globally for all projects 
on a build/sandbox server via `fin config set --global DOCKSAL_ENVIRONMENT=ci`. `fin` will then load
`docksal-ci.env` and/or `docksal-ci.yml` (these you do commit) on top of the default configuration files.

Note: `DOCKSAL_ENVIRONMENT` should not be set and will not work in the project's `docksal.env` file (this is by design).

### DOCKSAL_VERSION

`Default: master`

Allows for overriding the Docksal version used for checking for updates.

### DOCKSAL_STATS_OPTOUT 

`Default: 0` 

Allow for collecting of statistical usage of docksal. When set to `1` this will no longer send statistics.

### DOCKER_NATIVE

`Default: 0`

Designates whether to use Docker through VirtualBox or Native Docker. On Linux this is always set to `1`. Otherwise it is set to `0`.

For VirtualBox set to `0`.
For Docker Native set to `1`.

### DOCKSAL_DNS_UPSTREAM

Override the default DNS server that Docksal uses. For environments where access to Google DNS server (`8.8.8.8`) is blocked, it should be set to the LAN DNS server. This is often true for VPN users or users behind a corporate firewall.

### DOCKSAL_VHOST_PROXY_IP

Used to set the IP address for the Docksal reverse proxy to listen on. When `CI` variable is set to `true` this will be set to `0.0.0.0`.

### DOCKSAL_DNS_DOMAIN

`Default: docksal`

This is the domain to use which is tacked on to the end of the projects url.

### DOCKSAL_NO_DNS_RESOLVER

Allow disabling the DNS resolver configuration (in case there are issues with it). Set to `true` to activate.

### MYSQL_ROOT_PASSWORD

`Default: root`

This variable is mandatory and specifies the password that will be set for the MySQL root superuser account.

### MYSQL_ALLOW_EMPTY_PASSWORD

This is an optional variable. Set to `yes` to allow the container to be started with a blank password for the root user. NOTE: Setting this variable to `yes` is not recommended unless you really know what you are doing, since this will leave your MySQL instance completely unprotected, allowing anyone to gain complete superuser access.

### MYSQL_RANDOM_ROOT_PASSWORD

This is an optional variable. Set to `yes` to generate a random initial password for the root user (using pwgen). The generated root password will be printed to stdout (GENERATED ROOT PASSWORD: .....).

### MYSQL_ONETIME_PASSWORD

`Default: true`

When the variable is true (unless MYSQL_ROOT_PASSWORD is set or MYSQL_ALLOW_EMPTY_PASSWORD is set to true), the root user's password is set as expired and must be changed before MySQL can be used normally. This variable is only supported for MySQL 5.6 and later.

### MYSQL_INITDB_SKIP_TZINFO

Skip Timezone Checking when initializing the the DB engine.

### MYSQL_DATABASE

`Default:  default`

This variable allows you to specify the name of a database to be created on image startup. If a user name and a password are supplied with MYSQL_USER and MYSQL_PASSWORD, the user is created and granted superuser access to this database (corresponding to GRANT ALL). The specified database is created by a CREATE DATABASE IF NOT EXIST statement, so that the variable has no effect if the database already exists.

### MYSQL_USER

`Default:  user`

This is used to create a user, and the user is granted superuser permissions for the database specified by the `MYSQL_DATABASE` variable. Both `MYSQL_USER` and `MYSQL_PASSWORD` are required for a user to be created; if any of the two variables is not set, the other is ignored.

### MYSQL_PASSWORD

`Default:  user`

This is used to set the newly created user's password.

### MYSQL_PORT_MAPPING

`Default:  3306`

The port mapping to use for MySQL container, e.g., `33061:3306` will expose `3306` port as `33061` on the host. 

### POSTGRES_DB

`Default:  user`

This variable allows you to specify the name of a database to be created on image startup.

### POSTGRES_USER

`Default:  user`

This is used to create a user, and the user is granted superuser permissions for the database specified by the `POSTGRES_DB` variable.

### POSTGRES_PASSWORD

`Default:  user`

This is used to set the newly created user's password.

### PGSQL_PORT_MAPPING

`Default:  5432`

The port to use when setting up PostgreSQL.

### APACHE_BASIC_AUTH_USER

Username to use for basic authentication.

### APACHE_BASIC_AUTH_PASS

Password to use for basic authentication.

### GIT_USER_EMAIL

Overrides git `user.email` inside cli. This is picked up from host's git settings by default (`docksal/cli` v2.5+).

### GIT_USER_NAME

Overrides git `user.name` inside cli. This is picked up from host's git settings by default (`docksal/cli` v2.5+).

### HOST_UID

User ID for the Container User. On MacOS & Linux defaults to current user account `id -u`.

**WARNING: do not override this variable unless you know what you are doing.**

### HOST_GID

Group ID for the Container User. On MacOS & Linux defaults to current group account `id -g`.

**WARNING: do not override this variable unless you know what you are doing.**

### XDEBUG_ENABLED

`Default:  0`

Enables PHP XDebug Service for debugging. See [XDebug](/tools/xdebug/).

### SECRET_SSH_PRIVATE_KEY

Use to pass an additional private SSH key. The key is stored in `/home/docker/.ssh/id_rsa` inside `cli` and will be considered by the SSH client in addition to the keys loaded in `docksal-ssh-agent` when establishing a SSH connection from within `cli`.

### SECRET_ACAPI_EMAIL

Acquia Cloud API Email Address. See [Acquia Drush Commands](/tools/acquia-drush/).

### SECRET_ACAPI_KEY

Acquia Cloud API Key. See [Acquia Drush Commands](/tools/acquia-drush/).

### SECRET_TERMINUS_TOKEN

Token used for logging in to Pantheon's CLI Tool [Terminus](/tools/terminus/).

### CLI_USER

`Default:  docker`

User to be used for `fin exec`.

## Image Variables

The following variables are used to set images to a specified version. Using them will prevent the images from being udpated
when Docksal is updated.

### CLI_IMAGE

Defaults to the current cli image with the default php version defined in `~/.docksal/stacks/services.yml`. [See usage](/service/cli/settings)

### DB_IMAGE

Defaults to the current db image with the default mysql version defined in `~/.docksal/stacks/services.yml`. [See usage](/service/db/settings)

### WEB_IMAGE

Defaults to the current web image with the default apache version defined in `~/.docksal/stacks/services.yml`. [See usage](/service/web/settings)

## CI Variables

The following variables are generally only used within a CI system. They are primarily used for giving Docksal the ability to turn off and conserve resources.

### PROJECT_INACTIVITY_TIMEOUT

Defines the timeout of inactivity after which the project stack will be stopped (e.g., 0.5h).

### PROJECT_DANGLING_TIMEOUT

Defines the timeout of inactivity after which the project stack and code base will be entirely wiped out from the host (e.g., 168h). This requires PROJECTS_ROOT to be set.

**WARNING: use at your own risk!**

### PROJECTS_ROOT

Contains path to the project root directory.

### VIRTUAL_HOST_CERT_NAME

Tells `vhost-proxy` service which cert to use for the project. Certs are expected in `$HOME/.docksal/certs`. 
Project's `VIRTUAL_HOST` setting should match the certificate name.

For `VIRTUAL_HOST=example.com`, the following cert files must be present on the host:

```bash
$HOME/.docksal/certs/example.com.crt
$HOME/.docksal/certs/example.com.key
```

Also see [Default and custom certs for HTTPS](https://github.com/docksal/service-vhost-proxy#default-and-custom-certs-for-https).

### SANDBOX_PERMANENT

If set to `true`, the sandbox environment will not be removed event after the `PROJECT_DANGLING_TIMEOUT` timeout.

# ns8-gitlab

This is [Gitlab](https://about.gitlab.com/)-CE for Nethserver 8.

## Install

Instantiate the module with:

    add-module ghcr.io/jstammi/gitlab:latest 1

The output of the command will return the instance name.
Output example:

    {"module_id": "gitlab1", "image_name": "gitlab", "image_url": "ghcr.io/jstammi/gitlab:latest"}

## Configure

Let's assume that the gitlab1 instance is named `gitlab1`.

Launch `configure-module`, by setting the following parameters:
- `host`: The URL that the Gitlab-Installation is reachable.
- `registry_host`: The URL the Gitlab Registry is reachable
- `http2https`: If traefik should redirect to the https protocoll
- `lets_encrypt`: If Nethserver should get a Lets Encrypt HTTPS Cert

Example:

    api-cli run module/gitlab1/configure-module --data '{}'

The above command will:
- start and configure the gitlab instance
- (describe configuration process)
- ...

Send a test HTTP request to the gitlab backend service:

    curl http://127.0.0.1/gitlab/

No LDAP user or group is given admin role initially.
The passwor given on configuring is not (yet) respected.
But the `root` standard user's password is generated and written to
`/home/gitlabX/.local/share/containers/storage/volumes/gitlab-config/_data/initial_root_password`.

## Update module

You can forcefully update the module

```shell
api-cli run update-module --data '{"module_url":"ghcr.io/jstammi/gitlab:latest","instances":["gitlab1"],"force":true}'
```

## Smarthost setting discovery

Some configuration settings, like the smarthost setup, are not part of the
`configure-module` action input: they are discovered by looking at some
Redis keys.  To ensure the module is always up-to-date with the
centralized [smarthost
setup](https://nethserver.github.io/ns8-core/core/smarthost/) every time
gitlab starts, the command `bin/discover-smarthost` runs and refreshes
the `state/smarthost.env` file with fresh values from Redis.

Furthermore if smarthost setup is changed when gitlab is already
running, the event handler `events/smarthost-changed/10reload_services`
restarts the main module service.

See also the `systemd/user/gitlab.service` file.

This setting discovery is just an example to understand how the module is
expected to work: it can be rewritten or discarded completely.

## Uninstall

To uninstall the instance:

    remove-module --no-preserve gitlab1

## Testing

Test the module using the `test-module.sh` script:


    ./test-module.sh <NODE_ADDR> ghcr.io/jstammi/gitlab:latest

The tests are made using [Robot Framework](https://robotframework.org/)

## UI translation

Translated with [Weblate](https://hosted.weblate.org/projects/ns8/).

To setup the translation process:

- add [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- add your repository to [hosted.weblate.org]((https://hosted.weblate.org) or ask a NethServer developer to add it to ns8 Weblate project

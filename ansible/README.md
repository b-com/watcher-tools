# Setup

As a prerequisite on each targeted node, you should make sure of the following:

- The `python-pycurl` package should be installed
- Make sure the SSH public key has been added to the authorized users of the host. You can do that will following command:
``` shell
ssh-copy-id -i <my_public_key> user@host
```
# Usage
## Development

``` shell
$ ansible-playbook playbook.yml -i stage --ask-sudo-pass
```

In the above example, **stage** is you staging inventory file.

You can use the *-u REMOTE_USER* option in order to connect to an existing machine using a given user (which gives you an SSH access to the host).


## Production
``` shell
$ ansible-playbook -s playbook.yml -i production --ask-sudo-pass
```

Where **production** is you production inventory file.

If you only want to update metrics agents on compute nodes, you can target these hosts with the `compute-node` tag (or `metering-agent` tag):
``` shell
$ ansible-playbook -s playbook.yml -i production --ask-sudo-pass --tags=compute-node
```


oneill-server
=============

This repository contains ansible roles that allow the user to bootstrap,
provision and manage an oneill controlled docker host on a supported platform.


### Requirements

- A running Ubuntu server (capable of running docker) with SSH access enabled.
- Ansible must be installed on your local machine (as new a version as
  possible ideally, but at least >=1.8).
- A valid `oneill` configuration file.
- A valid `oneill` definitions file.

If you intend to route traffic to the server you should ensure that the
appropriate DNS records have been created and pointed at the server.

Note: oneill itself can run anywhere docker can really, but this repository
has some bits that are specific to an Ubuntu server like automatic security
updates by default.


## Logging

This repo contains a basic fluentd setup that will aggregate a configured set
of system log files, along with the output of any running containers and store
them on Google Cloud Storage. This configuration is simple to disable by
removing the role from `site.yaml` if you don't need it.

If you're going to use the configured logging setup, you'll need to tweak the
configuration values as seen in `hosts.example` and copy into `hosts`.


## Provisioning

Once your new instance is running we can use ansible to provision all the
required components on the box and set up our registry server.

First you'll need to edit and copy a few required files into place:

- `config.yaml.example`: A valid `oneill` config file should be placed at
  `roles/oneill/files/config.yaml`
- `definitions.yaml.example`: A valid `oneill` definitions file should be
  placed at `roles/oneill/files/definitions.yaml`

Next, copy `hosts.example` to `hosts` in the root of the repository and update
the contained values as required.

Once those files are in place the instance is ready to be provisioned. Ansible
makes this really easy:

```bash
$ ansible-playbook -i hosts site.yml
```

And you should be up and running. `oneill` will now load the latest site
definitions from the `/etc/oneill/definitions.yaml` file every 15 minutes. You
can use some additional method (`git pull` executed via cron job is the
simplest) to periodically update your site definitions from a central source.
The default playbook simply uploads a static file into place which works for
simple cases.


## Contributing

This is very much a work in progress, it will be expanded as time goes on. If
you have any problems/requests (bugs, feature requests, etc) then please feel
file to file a Github issue.

oneill-server
=============

This repository contains ansible roles that allow the user to bootstrap,
provision and manage an oneill controlled docker host on Google Compute
Engine.


### Requirements

- A Google Cloud project set up with billing enabled.
- Ansible must be installed on your local machine (as new a version as
  possible ideally, but at least >=1.8).
- A writable cloud storage bucket and interoperable access key must be created
  for storing logs.
- The Google Cloud SDK should be installed on your local machine.
- A valid SSL certificate/key for the domain you're planning to use.
- A valid `oneill` configuration file.
- A valid private key that can be used to check out a git repo full of site
  definitions.



## Bootstrapping

Before you can get started you'll need to create a dedicated GCE instance
(running at least Ubuntu 14.04). If you already have this set up, you can skip
to the "Provisioning" section below. Setting up a new instance is trivial with
the `gcloud` command line tool:

```bash
$ gcloud compute instances create example-server \
    --address example-address \
    --project example-project \
    --boot-disk-size 100GB \
    --image ubuntu-14-04 \
    --machine-type n1-standard-1 \
    --zone us-central1-a \
    --tags http-server \
    --tags https-server
```

You should ensure that the appropriate DNS records have been set to route
traffic for your desired domain to this newly created instance.



## Provisioning

Once your new instance is running we can use ansible to provision all the
required components on the box and set up our registry server.

First you'll need to copy a few required files into place:

- `.crt`: A valid SSL certificate bundle should be placed at `roles/nginx/files/labs-server.crt`
- `.pem`: A valid SSL private key file should be placed at `roles/nginx/files/labs-server.pem`
- `config.yaml`: A valid `oneill` config file should be placed at `roles/oneill/files/config.yaml`
- `definitions_repo.pem`: A valid deploy key for the definitions repo should be placed at `roles/oneill/files/definitions_repo.pem`

Next, copy `hosts.example` to `hosts` in the root of the repository and update
the contained values as required.

Once those files are in place the instance is ready to be provisioned. Ansible
makes this really easy:

```bash
$ ansible-playbook -i hosts site.yml
```

And you should be up and running. `oneill` will now load the latest site definitions from the `/etc/oneill/definitions/`
directory every few minutes. You should use some method (`git pull` executed via cron job is the simplest) to periodically
update your site definitions from a central source. The default playbook includes a cronjob that runs a `git pull` as `root`
every 3 minutes inside the `/etc/oneill/definitions/` directory.


## Contributing

This is very much a work in progress, it will be expanded as time goes on. If
you have any problems/requests (bugs, feature requests, etc) then please feel
file to file a Github issue.

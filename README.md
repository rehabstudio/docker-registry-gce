docker-registry-gce
===================

This repository contains a small number of shell scripts and ansible roles
that allow the user to bootstrap, provision and manage a docker private
registry on Google Compute Engine using Google Cloud Storage for persistence.


### Requirements

- A Google Cloud project set up with billing enabled.
- Ansible must be installed on your local machine (as new a version as
  possible ideally, but at least >=1.8).
- The Google Cloud SDK should be installed on your local machine.
- A valid SSL certificate/key for the domain you're planning to use.



## Bootstrapping

Before you can get started you'll need to create a dedicated GCE instance
(running at least Ubuntu 14.04). If you already have this set up, you can skip
to the "Provisioning" section below. Setting up a new instance is trivial with
the `gcloud` command line tool:

```bash
$ gcloud compute instances create example-registry \
    --address example-registry-ip \
    --project example-project \
    --image ubuntu-14-04 \
    --machine-type g1-small \
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

- `.htpasswd`: A valid NGINX htpasswd file should be placed at
  `roles/nginx/files/docker-registry.htpasswd`
- `.crt`: A valid SSL certificate bundle file should be placed at
  `roles/nginx/files/docker-registry.crt`
- `.pem`: A valid SSL private key file should be placed at
  `roles/nginx/files/docker-registry.pem`

Next, copy `hosts.example` to `hosts` in the root of the repository and update
the contained values as required.

Once those files are in place the instance is ready to be provisioned. Ansible
makes this really easy:

```bash
$ ansible-playbook -i hosts site.yml
```

And you should be up and running, you should now be able to log in with your
local docker client and use the registry as per the official docker documentation:

```bash
$ docker login registry.example.com
```


## Contributing

This is very much a work in progress, it will be expanded as time goes on. If
you have any problems/requests (bugs, feature requests, etc) then please feel
file to file a Github issue.

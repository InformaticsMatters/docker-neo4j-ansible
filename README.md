# Docker neo4j (Ansible)

[![Build Status](https://travis-ci.com/InformaticsMatters/docker-neo4j-ansible.svg?branch=master)](https://travis-ci.com/InformaticsMatters/docker-neo4j-ansible)
![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/informaticsmatters/docker-neo4j-ansible)

Ansible Playbooks (and Roles) for the docker-neo4j project.

The main `site` playbook allows for the deployment (and removal)
of a neo4j server and fragment network. The playbook employs the
docker-neo4j loader image to pull data from S3 prior to starting
a neo4j instance using the data.

Inspect `roles/graph/defaults/main.yaml` in order to configure the
deployment for your needs.

>   This role expects AWS and Kubernetes credentials, which are
    normally _injected_ into the play by our Ansible AWX (Tower) server.

## Usage
Run the same ansible the AWX server is likely to be running
(ideally from within a virtual environment)...

    $ conda activate docker-neo4j-ansible
    $ pip install -r requirements.txt

Declare cluster credentials that, for Kubernetes, consist of a `HOST`
and an `API_KEY`: -

    $ export K8S_AUTH_HOST=https://1.2.3.4:6443
    $ export K8S_AUTH_API_KEY=kubeconfig-user-tb4rm.c-tbgrt:12345678
    $ export K8S_AUTH_VERIFY_SSL=No 

>   The host and API key typically come from the kube config file,
    the API coming from the user token.

You'll also need AWS credentials to access the graph data (expected in
an S3 bucket): -

    $ export AWS_ACCESS_KEY_ID=1234
    $ export AWS_SECRET_ACCESS_KEY=abcdefghi

With credentials set you should be able to run the main playbook: -

    $ ansible-playbook site.yaml

>   The `ansible.cfg` and `inventory.yaml` files exist simply to avoid
    playbook warnings when running from the command-line within this project.
    When run from AWX (Ansible Tower) a 'real' inventory will be provided
    and `ansible.cfg` and `inventory.yaml` become unnecessary.

## Using private registries
If you've moved the graph and loader images to your own private registry
then you may need to provide a pull-secret for them. A variable is available
to define the image pull-secret name.

You will need to provide your own value for `graph_image_registry`
and then the name of your pull-secret.

For more detailed information refer to the documentation for the
`graph_preset_pullsecret_name` variable in `roles/graph/defaults/main.yaml`.

---

# Docker neo4j (Ansible)

![lint](https://github.com/InformaticsMatters/docker-neo4j-ansible/workflows/lint/badge.svg)

![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/informaticsmatters/docker-neo4j-ansible)

[![CodeFactor](https://www.codefactor.io/repository/github/informaticsmatters/docker-neo4j-ansible/badge)](https://www.codefactor.io/repository/github/informaticsmatters/docker-neo4j-ansible)

Ansible Playbooks (and Roles) for our [docker-neo4j] project.

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

You'll also need AWS credentials to allow the graph loader container,
part of the graph deployment, to access the graph data. The credentials
must give you permission to execute `s3:Get*` and `s3:List*`
actions in your chosen graph bucket and path: -

    $ export AWS_ACCESS_KEY_ID=1234
    $ export AWS_SECRET_ACCESS_KEY=abcdefghi

You might want to create a `parameters.yaml` file from the
`parameter-template.yaml` example. Adjust the values to suite
your needs and then, with AWS credentials set,
you should be able to run the main playbook: -

    $ ansible-playbook -e @parameters.yaml site.yaml

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
`all_image_preset_pullsecret_name` variable in `roles/graph/defaults/main.yaml`.

---

[docker-neo4j]: https://github.com/InformaticsMatters/docker-neo4j

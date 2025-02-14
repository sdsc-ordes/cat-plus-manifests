# Cat+ Manifests

> [!WARNING]
> This is a WIP repository, it is not yet stable.

## Summary

This repository contains all the manifests for Cat+ to deploy on Kubernetes. 

Cat+ is composed of automated pipelines, a front-end and a databases. All of these need to be deployed on Kubernetes. This repository allows for maintenance and easy deployment. 

## Installation guidelines

> [!NOTE] We assume that you already have access to a kubernetes cluster and that nix is installed ([install here](https://github.com/DeterminateSystems/nix-installer?tab=readme-ov-file#determinate-nix-installer)).

We provide a nix-based development shell which includes all dependencies required to manage and deploy manifests.
To build and enter the development shell, use `just nix-develop`.

## How to Use

### Deploying resources

We use kustomize to configure and deploy resources. To deploy all resources, use `just deploy`.

### Secret management

Secrets are encrypted using sops and age. A just module is provided to simplify operations.
Type `just secrets` to see the list of operations available.

#### Adding a recipient

To add a new recipient (i.e. someone whose key can be used to decrypt secrets), take the following steps.

1. New recipient runs `just secrets generate-key` and sends the public key (only!) to an existing recipient (see `.sops.agekey``).
2. Existing recipient adds the public key to `.sops.yaml` (keys are comma-separated)
3. Existing recipient runs `just update-keys` to re-encrypt secrets with the new key.
4. Existing recipient commits changes.

#### Deploying secrets

Running `just secrets deploy` will decrypt the secrets into a temporary file, deploy them with kubectl and delete the temporary file.

#### Editing secrets

To edit secrets:

1. Run `just secrets decrypt` and open the newly created `secrets/secrets.dec.yaml` in your editor.
2. Once the changes are made, re-encrypt them using `just secrets encrypt`.
3. Commit changes.

### Template management

Some service manifests are based on external helm charts. We render these templates once from upstream and persist the rendered templates in this repository.
Kubectl is then used with kustomize for the deployment. Periodically, we may want to refresh the rendered manifests due to updates in the upstream template.

To do so, run:

```shell
just render <dir>
```

## Contribute

To be defined.

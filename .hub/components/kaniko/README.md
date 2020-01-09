# Kaniko

This component build a docker image out of user source code in the git.

During the deploy time hub starts a Kubernetes job with two containers.
1. `git-sync`: to clone a user git repository
2. `kaniko`: to build and push a docker image

This component will check where is a docker registry pull secret

## Future evolution
- [ ] add support for ECR
- [ ] add support for Gitlab
- [ ] add troubleshooting section

## Configuration
Most important parameters can be found below
```yaml
parameters:
- name: component.kaniko
  parameters:
  - name: destination
    brief: Name of the docker image and destination to do a docker push
  - name: contextDir
    brief: directory inside of a git repository (default root)
- name: component.docker.registry
  parameters:
  - name: url
    brief: docker registry URL
  - name: pullSecret.name
    brief: name of a docker registry pull secret
  - name: pullSecret.namespace
    brief: name of a docker registry pull secret namespace
```

## Troubleshooting

Most common reason to fail is either: incorrect `component.kaniko.contextDir` or Dockerfile under `component.kaniko.dockerfile` cannot be found... To solve this and any other issue do the following:

1. Check the deployment log on console
2. Change value of param: `component.kaniko.image` to `gcr.io/kaniko-project/executor:debug`
3. Change `kaniko-job.yaml.tempalte` to:

```yaml
...
containers:
- name: kaniko
  image: ${component.kaniko.image}
  command: [sleep, '600']
  # args: [
  #   '--cache',
  #   '--destination', '${component.kaniko.destination}',
  #   '--dockerfile', '${component.kaniko.dockerfile}',
  #   '--context', '/build/workspace/${component.kaniko.contextDir}',
  # ]
```

4. Run `make elaborate deploy` 
5. On the stage: `Waiting for job qwerty to complete ...` CTRL+C
6. Run `kubectl get pods` and get the pod name
7. Run `kubectl exec -it KANIKO_POD_NAME sh`
8. Code of the repo should be in `/build/git/workspace`
9. To test kaniko execution run: `executor --help`

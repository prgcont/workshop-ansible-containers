# Building container images with Ansible

This workshop introduces the concept of building container images by using
Ansible playbooks as the definition format. We'll use
[ansible-bender](https://github.com/TomasTomecek/ansible-bender) to do the job,
which utilizes [ansible](https://github.com/ansible/ansible) and
[buildah](https://github.com/containers/buildah) under the hood. For more info,
see [this slide
deck](https://tomastomecek.github.io/speaks/2019-prgcont-ansible-bender/#1).


## Requirements

* [buildah](https://github.com/containers/buildah/blob/master/install.md) is installed
* [podman](https://github.com/containers/libpod/blob/master/install.md) is installed
* [ansible-buildah](https://github.com/TomasTomecek/ansible-bender#installation) is installed
* python 3.6+
* base image with python (can be 2)
* ansible 2.6+


## Containerizing your tests

One of the common tasks in our team is to containerize our upstream test suite.
Running tests in containers is awesome because the environment is the same
no matter where it runs.

Let's run tests in a container for an existing project of mine:
[tmux-top](https://github.com/TomasTomecek/tmux-top). It is written in Go.

All we need to do is just write one Ansible playbook. Let's start with this one:
```yaml
- hosts: all
  vars:
    ansible_bender:
      base_image: "fedora:29"

      target_image:
        name: tmux-top-tests
        environment:
          A: B
  tasks: []
```

Our task now is to clone the upstream repo -
https://github.com/TomasTomecek/tmux-top/, and run the tests using `make tests`.

For the complete solution, check [tmux-top-tests.yaml](/tmux-top-tests.yaml).

You can then use the playbook to build the image:
```
$ ansible-bender build tmux-top-tests.yaml
```

And then run the tests afterwards:
```
$ podman run --rm -ti tmux-top-tests bash
$ cd /go/src/github.com/TomasTomecek/tmux-top/
$ make test
```

## Hugo in a container

Let's containerize static site generator - [Hugo](https://gohugo.io/).

The complete solution is available as [hugo.yaml](/hugo.yaml).

If we wanted to run this container in rootless podman, we would need to have
podman 1.1+ because port forwarding for rootless is available since this
version.

Let's run it in docker then! We need to push the image to dockerd first:
```
$ ansible-bender push docker-daemon:hugo:latest
```

Let's make sure that the metadata are correct:
```
$ docker inspect hugo
```

We can run hugo now:
```
$ docker run -p 80:80 hugo
```

...and use sen to check it out:
```
$ sen
```

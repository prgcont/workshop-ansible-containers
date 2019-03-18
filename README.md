# Building container images with Ansible

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

TBD


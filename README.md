# Role Name

This role installs [Kata Containers](https://katacontainers.io/) and should work on most
distributions.
This role **does not** configure [containerd](https://containerd.io/) or [CRI-O](https://cri-o.io/)
in any way.
You have to configure them yourself in order to use Kata Containers.
An example for containerd can be found below.

## How it works

This role simply downloads a Kata Containers release of your choice (in `.tar.xz` format).
The role then creates

- a symlink at `/opt/kata` pointing to the downloaded release, and
- a symlink at `/usr/local/bin/<binary>` for each binary that should be in the PATH.

## Example Playbook

The following example playbook assumes that you cloned this role to `roles/kata_containers`
(i.e. the name of the role is `kata_containers` instead of `ansible_kata_containers`).

```yml
- hosts: kube01
  roles:
    - role: kata_containers
      global_cache_dir: "{{ lookup('env', 'HOME') }}/.cache/stuvus"
      kata_containers_archive_url: https://github.com/kata-containers/kata-containers/releases/download/2.0.1/kata-static-2.0.1-x86_64.tar.xz
      kata_containers_checksum: sha256:1ac8aefe10687360fc55dc1b0f9a06f6dad695213e811f2c3e222b088779edb1
```

## Role Variables

The role uses the following variables.
All of them are **required**

| Name                          | Description                                                |
| ----------------------------- | ---------------------------------------------------------- |
| `kata_containers_archive_url` | URL to Kata Containers release archive in `.tar.xz` format |
| `kata_containers_checksum`    | Checksum of the downloaded archive                         |

## containerd configuration

This role does not configure containerd.
You can use Kata Containers (deployed by this role) with containerd by adding the following to your
containerd configuration.

```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.kata]
runtime_type = "io.containerd.kata.v2"

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.kata.options]
ConfigPath = "/opt/kata/share/defaults/kata-containers/configuration.toml"
```

## License

This work is licensed under the [MIT License](./LICENSE).

## Author Information

- [Sebastian Hasler (haslersn)](https://github.com/haslersn) _sebastian.hasler at stuvus.uni-stuttgart.de_

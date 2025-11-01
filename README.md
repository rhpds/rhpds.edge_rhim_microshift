# Ansible Collection - rhpds.edge_rhim_microshift

This collection provides Ansible roles for setting up and configuring a Red Hat MicroShift environment.

## Roles

### registry

This role sets up a local container registry and optionally mirrors a list of container images to it.

#### Requirements

- A host named `registry` must be defined in your Ansible inventory.
- `podman` and `skopeo` need to be installed on the `registry` host.

#### Role Variables

- `registry_mirror_images`: A boolean that determines whether to mirror container images to the local registry. Defaults to `true`.
- `registry_images_to_mirror`: A list of container images to mirror. The role provides a default list of images for MicroShift.
- `ocp4_pull_secret`: Your pull secret for `quay.io` and other registries, in JSON format. This is required to pull the default images.

#### Example Playbook

**Default Usage (with image mirroring):**

```yaml
- hosts: registry
  roles:
    - role: registry
```

**Customizing Mirrored Images:**

You can override the default list of images by setting the `registry_images_to_mirror` variable.

```yaml
- hosts: registry
  roles:
    - role: registry
  vars:
    registry_images_to_mirror:
      - "my.registry.com/my/image:latest"
      - "another.registry.com/another/image:1.2.3"
```

**Disabling Image Mirroring:**

If you only want to set up the registry without mirroring images, set `registry_mirror_images` to `false`.

```yaml
- hosts: registry
  roles:
    - role: registry
  vars:
    registry_mirror_images: false
```

## Example Usage

1.  **Create an inventory file (`inventory.yml`):**

    ```yaml
    all:
      hosts:
        registry:
          ansible_host: 192.168.1.100
    ```

2.  **Create a playbook file (`playbook.yml`):**

    To mirror images from private registries, you need to provide a pull secret. You can get your pull secret from [Red Hat OpenShift Cluster Manager](https://console.redhat.com/openshift/install/pull-secret).

    ```yaml
    ---
    - hosts: registry
      vars:
        ocp4_pull_secret: '{"auths":{"quay.io":{"auth":"<your_quay.io_pull_secret>"}}}'
      roles:
        - role: registry
    ```

3.  **Run the playbook:**

    ```bash
    ansible-playbook -i inventory.yml playbook.yml
    ```

## Notes

- This role uses `skopeo` to mirror images. It handles multi-architecture images and preserves the repository path.
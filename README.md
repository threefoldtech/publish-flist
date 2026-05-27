# Publish Flist

A GitHub Action for publishing, promoting, and managing flists — lightweight container formats used for deploying workloads without heavy image downloads.

## What this is

This repository provides a GitHub Action that automates the packing and publishing of flists to a container registry (hub). Flists are file lists that describe the contents of a container image in a compact format, allowing nodes to fetch only the files they need on demand. This action supports publishing new flists, promoting existing ones between repositories, renaming, symlinking, cross-linking, deleting, reading symlinks, merging multiple flists, and tagging.

## What this repository contains

- **GitHub Action definition** — A composite action exposing nine operations:
  - `publish` — Pack and upload a new flist.
  - `promote` — Promote an flist from one repository to another.
  - `rename` — Rename an flist within your repository.
  - `symlink` — Create a symlink to an flist within your repository.
  - `crosslink` — Create a symlink across repositories.
  - `delete` — Delete an flist.
  - `readlink` — Resolve the target of a symlink.
  - `merge` — Merge multiple flists into a single flist.
  - `tag` / `crosstag` — Tag an flist or create a cross-repository tag.

## Usage

### Publish

This step packs your files and uploads the resulting flist to the hub.

You can add an optional `root` parameter to choose the root directory. By default `/github/workspace` is used.

The token needs to be a valid Hub JWT token.

```yaml
- name: Publishing flist
  uses: threefoldtech/publish-flist@master
  with:
    action: publish
    name: demo-from-action.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Promote

This step promotes an flist from a repository to another. You need to choose your cross-user via the `user` input.

The `target` input will be the name of the promoted flist; the `name` contains the original file and repository: `repository/source.flist`.

```yaml
- name: Promoting flist
  uses: threefoldtech/publish-flist@master
  with:
    action: promote
    user: tf-official-apps
    name: maxux/demo-from-action.flist
    target: demo-action-promoted.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Rename

This step renames an flist to another name within your repository. You can use the `user` input to switch to another user before renaming.

The input `target` will be the new name.

```yaml
- name: Rename flist
  uses: threefoldtech/publish-flist@master
  with:
    action: rename
    user: tf-official-apps
    name: my-original.flist
    target: my-new-name.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Symlink

This step symlinks an flist to another name within your repository. You can use the `user` input to switch to another user before symlinking.

The input `target` will be the link name.

```yaml
- name: Symlink flist
  uses: threefoldtech/publish-flist@master
  with:
    action: symlink
    user: tf-official-apps
    name: demo-action-promoted.flist
    target: final:symlinked-master.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Cross-Link

This step symlinks across repositories (create a symlink in your user, pointing to a file of another user). You can use the `user` input to switch to another user before symlinking.

The input `target` will be the destination (link to).

```yaml
- name: Crosslink flist
  uses: threefoldtech/publish-flist@master
  with:
    action: crosslink
    user: tf-official-apps
    name: my-crosslink-file.flist
    target: maxux/nginx-latest.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Delete

This step deletes one of your flists. You can use the `user` input to switch to another user before deleting.

```yaml
- name: Delete flist
  uses: threefoldtech/publish-flist@master
  with:
    action: delete
    user: tf-official-apps
    name: demo-action-promoted.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Readlink

This step reads the symlink target of an flist. Any flist can be resolved. The resulting name (if the flist is a symlink) will be set on the output `linkpoint`, which you can reuse later.

```yaml
- name: Readlink of the current build flist
  uses: threefoldtech/publish-flist@master
  id: readlink
  with:
    action: readlink
    user: tf-official-apps
    name: demo-action-symlinked.flist
    token: ${{ secrets.HUB_TOKEN }}

- name: Do something with the readlink
  run: |
    echo "Symlink: ${{ steps.readlink.outputs.linkpoint }}"
```

### Merge

This step merges multiple flists present on the hub into a single one. The resulting name (the final flist) is specified by the `name` field. The list of flists you want to merge is specified in `target` and separated by spaces.

```yaml
- name: Merge service with ubuntu
  uses: threefoldtech/publish-flist@master
  with:
    action: merge
    user: tf-official-apps
    name: my-merged-service.flist
    target: tf-bootable/ubuntu:18.04.flist maxux/my-service.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Tagging

This step tags a remote flist into your own tagging repository. You can use the `user` input to switch to another user before tagging.

The input `name` will be the tag name and link name (e.g., `v1.0.0/somelink`).
The input `target` will be the destination (link to).

```yaml
- name: Tagging flist
  uses: threefoldtech/publish-flist@master
  with:
    action: tag
    user: tf-official-apps
    name: v1.0.0/nginx
    target: maxux/nginx-latest.flist
    token: ${{ secrets.HUB_TOKEN }}
```

### Cross-Tagging

This step symlinks a tag into your repository. You can use the `user` input to switch to another user before symlinking.

Warning: `name` must not end with `.flist`!

The input `target` will be the destination (link to).

```yaml
- name: Crosstag a tag
  uses: threefoldtech/publish-flist@master
  with:
    action: crosstag
    user: tf-official-apps
    name: my-crosstag-name
    target: maxux/v2.0.0
    token: ${{ secrets.HUB_TOKEN }}
```

## Role in the stack

This action works with the Zero-OS Hub, a container registry for flists. Flists are the lightweight container format used on Zero-OS nodes for deploying workloads. By publishing flists through this action, CI/CD pipelines can automatically package and distribute container images to the hub, making them available for deployment across the infrastructure.

## ZOS / Zero-OS

ZOS, also known as Zero-OS, is the operating system layer used to run and manage nodes. It provides the low-level runtime environment for workloads, networking, storage, and automation. Flists are the native container format consumed by ZOS for deploying applications.

## Relation to ThreeFold

This technology is used within the ThreeFold ecosystem and was first deployed on the ThreeFold Grid. The component itself is designed as reusable infrastructure technology and should be understood by its technical function first, independent of any specific deployment.

## Ownership

This repository is owned and maintained by TF-Tech NV, a Belgian company responsible for the development and maintenance of this technology.

## License

This project is licensed under the Apache License 2.0 — see the [LICENSE](LICENSE) file for details.
Copyright (c) TF-Tech NV.

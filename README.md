# Publish your work on the Zero-OS Hub

You can use this GitHub action to automatically pack
and publish your stuff into the Zero-OS hub.

You can (for now) use 7 differents action within this action:

## Publish

This step can be used to pack your stuff and upload the
resulting flist into the hub (playground).

You can add an optionnal `root` parameter to choose the root directory.
By default `/github/workspace` is used.

The token needs to be a valid Hub JWT token issued from ItsYou.Online

```yaml
- name: Publishing flist
  uses: threefoldtech/publish-flist@master
  with:
    action: publish
    name: demo-from-action.flist
    token: ${{ secrets.HUB_TOKEN }}
```

## Promote

This step can be used to promote an flist from a repository
to another. You need to choose your cross-user via `user` input.

The `target` input will be the name of the promoted flist, the `name`
contains the original file and repository `repository/source.flist`

The token needs to be a valid Hub JWT token issued from ItsYou.Online

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

## Rename

This step can be used to rename an flist to another name, within your repository.
You can use the `user` input to switch to another user before symlinking.

The input `target` will be the new name.

The token needs to be a valid Hub JWT token issued from ItsYou.Online

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


## Symlink

This step can be used to symlink an flist to another name, within your repository.
You can use the `user` input to switch to another user before symlinking.

The input `target` will be the link name.

The token needs to be a valid Hub JWT token issued from ItsYou.Online

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

## Cross-Link

This step can be used to symlink across repository (create a symlink in your user, pointing
to a file of another user).
You can use the `user` input to switch to another user before symlinking.

The input `target` will be the destination (link to).

The token needs to be a valid Hub JWT token issued from ItsYou.Online

```yaml
- name: Crisslink flist
  uses: threefoldtech/publish-flist@master
  with:
    action: crosslink
    user: tf-official-apps
    name: my-crosslink-file.flist
    target: maxux/nginx-latest.flist
    token: ${{ secrets.HUB_TOKEN }}
```

## Delete

This step can be used to delete one of your flist.
You can use the `user` input to switch to another user before deleting.

The token needs to be a valid Hub JWT token issued from ItsYou.Online

```yaml
- name: Delete flist
  uses: threefoldtech/publish-flist@master
  with:
    action: delete
    user: tf-official-apps
    name: demo-action-promoted.flist
    token: ${{ secrets.HUB_TOKEN }}
```

## Readlink

This step can be used to read the symlink target of an flist, any flist can be resolved.
The resulting name (if the flisti is a symlink) will be set on the output `linkpoint` which
you can reuse later.

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
    echo "Symlink: ${{ steps.readlink.outputs.linkpoint }}
```


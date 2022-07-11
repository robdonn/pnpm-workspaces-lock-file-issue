# Workspaces and Specifiers issue

When working with workspaces and `pnpm` the workspaces are shown in the `pnpm-lock.yaml` file as it detects the `package.json` file inside the workspace.

The problem is that it also detects the `package.json` files inside other directories not listed as workspaces and includes them in the `pnpm-lock.yaml` file, even when in a directory which is specified in a `.gitignore` file.

## This demo

This is a `pnpm` workspace monorepo. There are 2 directories:

- `/app` is an application being developed in the repo.
- `/dist` is a local build output directory of `/app`. Typically this directory would be listed in the `.gitignore` file.

The `/app` directory is the only one listed in the `pnpm-workspace.yaml` file and is referenced as a workspace in the root `package.json` file.

Despite this, both the `/app` and `/dist` directories are being listed in the `pnpm-lock.yaml` file.

```yaml
lockfileVersion: 5.4

importers:
  .:
    specifiers:
      app: workspace:*
    dependencies:
      app: link:app

  app:
    specifiers: {}

  dist:
    specifiers: {}
```

## Why is this a problem?

The `/dist` app is an example of a build folder which is the output of `/app`. During local development a user may build their app locally and have this directory on their system.

Because this directory is usually defined in a `.gitignore` it will not always exist, so different users will end up with a difference in the `pnpm-lock.yaml` file.

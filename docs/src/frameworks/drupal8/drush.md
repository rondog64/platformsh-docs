---
title: "Working with Drush in Drupal 8"
weight: 2
sidebarTitle: "Drush"
---

Drush is a command-line shell and scripting interface for Drupal, a veritable Swiss Army knife designed to make life easier for those who spend their working hours hacking away at the command prompt. Drush commands can, for example, be used to clear the Drupal cache, run module and database updates, revert features, perform database imports and dumps, and a whole lot more. You can reference the full set of Drush commands at [Drush.org](http://www.drush.org)

If you have never used Drush before, you can learn more about it on the [Drush GitHub Repository](https://github.com/drush-ops/drush#description)

* Platform.sh's Drupal templates have Drush installed.
* Drush commands can be run in build, deploy, and post_deploy hooks, although remember that as the database is not available at build time many Drush commands will not work at build time.
* In addition, you can use the [Platform.sh CLI]({{< relref "/development/cli/_index.md" >}}) to set up Drush aliases easily for all of your project's environments. See the section below on [use drush aliases]({{< relref "#use-drush-aliases" >}}).

{{< note >}}
Platform's CLI requires **Drush 6 or higher**.
{{< /note >}}

## Installing Drush

### Use the Platform.sh-provided Drupal examples

If you started your project from one of Platform.sh's Drupal templates then Drush is already installed and configured.  There is nothing else for you to do.

### Install Drush in custom projects using _Composer_

This is the recommended approach.

Run this command in the project's repository root folder:

```bash
$ composer require drush/drush
```

Commit the `composer.json` and `composer.lock` files and push.

Drush will then be available at `vendor/bin/drush`, in the exact same version on your local system and on Platform.sh.

### Install Drush in custom projects using _Build Dependencies_

Platform.sh supports installing some as system-level tools as [build dependencies]({{< relref "/configuration/app/build.md#build-dependencies" >}}).

To install Drush using this method, use the following in `.platform.app.yaml`

```yaml
dependencies:
    php:
        "drush/drush": "^8.0"
```
## Accessing Drush within the project

For Drush to be available on the command line, **it must be added to the project's $PATH**.

Add a new file named `.environment` to the root of your your project's git repository with this code:

```bash
# Statements in this file will be executed (sourced) by the shell in SSH
# sessions, in deploy hooks, in cron jobs, and in the application's runtime
# environment. This file must be placed in the root of the application, not
# necessarily the git repository's root. In case of multiple applications,
# each application can have its own .environment file.

# Allow executable app dependencies from Composer to be run from the path.
if [ -n "$PLATFORM_APP_DIR" -a -f "$PLATFORM_APP_DIR"/composer.json ] ; then
  bin=$(composer config bin-dir --working-dir="$PLATFORM_APP_DIR" --no-interaction 2>/dev/null)
  export PATH="${PLATFORM_APP_DIR}/${bin:-vendor/bin}:${PATH}"
fi
```

## Install Drush locally

You can install drush _globally_ with Composer. This does not add it to your project.

```bash
$ composer global require drush/drush
```

At the end of the installation, you should be able to run:

```bash
$ drush
```

## Use drush aliases

### Create Drush aliases

[Drush aliases](http://drush.readthedocs.org/en/master/usage/index.html#site-aliases) make it easy to manage your development websites. Here's an example of a [Drush alias file](https://github.com/drush-ops/drush/blob/8.x/examples/example.aliases.drushrc.php).

The Platform.sh CLI generates Drush aliases for you automatically when you run `platform get [project_id]`.

To see the aliases that are created, run `platform drush-aliases` and you should get output similar to that below:

```bash
$ platform drush-aliases
Aliases for My Site (tqmd2kvitnoly):
    @my-site._local
    @my-site.master
    @my-site.staging
    @my-site.sprint1
```
### Recreating Drush aliases

To recreate existing aliases, or after pushing a new branch via git to create the new alias, run:

```bash
platform drush-aliases -r
```

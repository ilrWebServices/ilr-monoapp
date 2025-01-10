# ILR Monoapp

A collection of small web projects hosted on platform.sh. Each application is included as a git submodule, and platform is configured for [multi-app](https://docs.platform.sh/create-apps/multi-app.html).

# Requirements

- `git`

# Setup

To clone this repository along with the embedded submodules, use the `--recurse-submodules` argument. For example:

```
$ git clone --recurse-submodules git@github.com:ilrWebServices/ilr-monoapp.git
```

Add a git remote to the Platform.sh project:

```
$ git remote add platform b7zqcwm47vwbc@git.us.platform.sh:b7zqcwm47vwbc.git
```

# Adding applications

New applications are added using several steps:

## 1. Add the application as a git submodule

E.g. `git submodule add git@github.com:ilrWebServices/scheinman-neutrals-web.git`

## 2. Add the application to the platform.sh config file

Most application configuration is defined in `.platform/applications.yaml`.

This file is essentially a collection of `.platform.app.yaml` files in any array. Each array item should start with the `name` property with a short name of the application, e.g. `- name: neutrals`.

> [!NOTE]
> Choose the short name carefully, because it's hard to change later. Don't make a mistake like we made by calling an app `drupal8`, which makes a lot less sense by the time you've upgraded to Drupal 10.

### Required platform.sh files

Platform.sh documentation recommends that some files, like `.environment` and scripts, be placed in the root directory of an application. For the applications are running in this multi-app, we try to avoid hosting-specific configuration in their respective repos.

Instead, we add the required files during the build hooks for the applications. To avoid complicated quote escaping, the files are base64 encoded.

The [Encode Decode extension](https://github.com/mitchdenny/ecdc) for VS Code is useful for editing these embedded files.

Some files, like `web/sites/default/settings.platformsh.php` in Drupal sites, should probably remain in the application repo just to make life easier.

## 3. Add any necessary route(s)

[Platform multi-app routes](https://docs.platform.sh/create-apps/multi-app/routes.html) go in `.platform/routes.yaml`.

## 4. Add any necessary services

Definitions for services such as databases, caching, etc should be added to `.platform/services.yaml`. Try to keep these to a minimum, however, since these are generally small applications and we want to keep hosting resources low. Most applications will use sqlite over mariadb, for example.

# Updating applications

To update the applications, run the following:

```
$ git pull origin
$ git submodule update --remote
```

This shortcut only works if the applications use the same branch names (`main`, `develop`, etc.) as this repository. Of course, submodules can always be configured independently checking them out at specific versions, but this way reduces mistakes.

Then run a `git diff --submodules` to see a list of the new commits in each project. You can use that output in your commit message.

Commit the change to the submodule if the diff looks correct.

# Deployment

After updating the project submodule(s), push the project to the platform remote:

```
git push platform
```

Be sure to push your changes to `origin`, too, via `git push origin`. While not essential, it provides a remote backup of the repository outside of our hosting provider.

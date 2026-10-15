# Releasing

## Source code

In `check_pgbouncer`:
  * edit variable `$VERSION`
  * update the version and release date at the end of the inline documentation
    * date format: `LC_TIME=C date +"%a %b %d %Y"`

In `CHANGELOG.md`, add a changelog entry for the new release with the format:

~~~
YYYY-MM-DD vX.Y:

  * add: description...
  * ...
  * change: description...
  * ...
  * fix: description...
  * ...
~~~

Update documentation using the following commands:

~~~
pod2text check_pgbouncer > README
podselect check_pgbouncer > README.pod
~~~

Update the `contributors` file with new contributors.

## Run tests

Run the tests against all possible pgBouncer releases. At least the one
officially supported. Fix code or tests before releasing.

## Commit

Commit all these changes together with commit message: `Release X.Y`.

## Tagging and building tar file

Directly into the official repo:

~~~
TAG=REL0_1
git tag -a $TAG  <Release commit number>
git push --tags
git archive --prefix=check_pgbouncer-$TAG/ -o /tmp/check_pgbouncer-$TAG.tgz $TAG
~~~

## Release on github

  - Go to https://github.com/OPMDG/check_pgbouncer/releases
  - Edit the release notes for the new tag
  - Set "check_pgbouncer $VERSION" as title, eg. "check_pgbouncer 2.4"
  - Here is the format of the release node itself:
    ~~~
    YYYY-MM-DD -  Version X.Y

    Changelog:
      * item 1
      * item 2
      * ...
    ~~~
  - Upload the tar file
  - Save
  - Check or update https://github.com/OPMDG/check_pgbouncer/releases

## Submit a news on postgresql.org

* login: https://www.postgresql.org/account/login/?next=/account/
* go https://www.postgresql.org/account/edit/news/
* click "Submit News Article"
* organisation: check_pgbouncer project
* Title: "Release: check_pgbouncer 2.4"
* Content:

  ~~~
  check_pgbouncer version 0.1 released
  ========================

  check\_pgbouncer is a PostgreSQL plugin for Nagios. This plugin is written with a focus
  on a rich perfdata set. Every new features of PostgreSQL can be easily monitored with
  check\_pgbouncer.

  Changelog :

  * ...
  * ...
  * ...


  Here are some useful links:

  * github repo: [https://github.com/OPMDG/check_pgbouncer](https://github.com/OPMDG/check_pgbouncer)
  * reporting issues: [https://github.com/OPMDG/check_pgbouncer/issues](https://github.com/OPMDG/check_pgbouncer/issues)
  * latest release: [https://github.com/OPMDG/check_pgbouncer/releases/latest](https://github.com/OPMDG/check_pgbouncer/releases/latest)
  * contributors: [https://github.com/OPMDG/check_pgbouncer/blob/master/contributors](https://github.com/OPMDG/check_pgbouncer/blob/master/contributors)

  Thanks to all the contributors!
  ~~~

* check "Related Open Source"
* click on submit
* wait for moderators...

## Tweets & blogs

Make some noise...

## Start the work on the next release

In `check_pgbouncer`:
  * edit variable `$VERSION`, and add "dev" to the new version (for example
    2.13dev)


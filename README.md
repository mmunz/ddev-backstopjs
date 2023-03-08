[![tests](https://github.com/mmunz/ddev-backstopjs/actions/workflows/tests.yml/badge.svg)](https://github.com/mmunz/ddev-backstopjs/actions/workflows/tests.yml) ![project is maintained](https://img.shields.io/maintenance/yes/2024.svg)

## ddev-backstopjs

This is a ddev-addon for [backstop.js](https://github.com/garris/BackstopJS), a visual regression testing tool.
Backstop is executed in a docker container based on the official [backstopjs docker image](https://hub.docker.com/r/backstopjs/backstopjs).

This addon just provides tha basics to run backstopjs. No backstopjs config is included.

## Getting started

Install this addon with

```shell
ddev get mmunz/ddev-backstopjs
```

After that you need to restart the ddev project:

```shell
ddev restart
```

## Using backstopjs

### Configuration

By default, the backstop tests are expected in $DDEV_APPDIR/tests/backstop.

Provide your own backstop.js or backstop.json configs here.

Hint: have a look at the config the author uses: https://github.com/mmunz/backstopjs-config 

Alternatively you can create a simple backstop.json config with:

```shell
ddev backstop init
```

### Run tests

After the config was created it is time to run the tests:

Create reference screenshots:

```shell
ddev backstop reference
```

Create test images and compare to reference screenshots:

```shell
ddev backstop test
```

If your config file is not 'backstop.json' you need to use the --config argument, e.g. --config=backstop.js

### View test results

The backstop commands 'backstop remote' and 'backstop openReport' do not work in this setup (PR welcome!),
instead just open the generated HTML-Report with your browser, e.g.:

```shell
open tests/backstop/backstop_data/_mytestproject_/html_report/index.html 
```

## Changes to the original docker image

The backstopjs docker image is extended with some functions using a custom docker build, see An [backstopBuild/Dockerfile](Dockerfile)
and uses a custom [backstopBuild/entrypoint.sh](entrypoint).

In the Dockerfile the following is added/changed:

- add the custom entrypoint.sh to the image
- delete the default 'node' user with uid 1000 and add current ddev user
- install the [https://www.npmjs.com/package/minimist](minimist) npm package globally. This is not needed by default
  but very handy to parse command line args for more complex custom backstopjs configs.

The entrypoint is responsible for:

- add /etc/hosts entries for all hosts configured in the ddev web container automatically
- add sleep command to keep the container running

## Advanced

### How to add additional hostnames?

If you want to test hosts not configured in the web container, you need to use external_links in
[docker-compose.backstop.js](docker-compose.backstop.js). Do not forget to remove the #ddev-generated line!
See: [https://ddev.readthedocs.io/en/latest/users/usage/faq/#features-requirements](ddev FAQ: Can different projects communicate with each other?)

### Change backstop tests directory
Per default the backstop directory containing backstop config etc. is expected in your project directory (besides the
.ddev folder) in the directory *tests/backstop*.

If you want to change that edit the file [docker-compose.backstop.yaml](docker-compose.backstop.yaml) and
change the line in volumes to the path you want to use, move the files to the new directory and restart ddev.

Make sure to remove the #ddev-generated line from the file to prevent ddev from maging changes to it.

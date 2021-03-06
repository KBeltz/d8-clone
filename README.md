# Drupal 8 Creighton University

This project encompasses the Drupal 8 multisite platform for Creighton University marketing sites.

## Table of Contents

* [Getting Started](#getting-started)
* [Virtual Machine](#virtual-machine)
* [Get Code](#get-code)
* [Local Environment Setup](#local-environment-setup)
* [Further Setup](#further-setup)
* [Additional Documentation](#additional-documentation)
* [Working With a BLT Project](#working-with-a-blt-project)
* [Resources](#resources)
* [Platform Documentation](#platform-documentation)

## Getting Started

This project is based on BLT, an open-source project template and tool that enables building, testing, and deploying Drupal installations following Acquia Professional Services best practices. While this is one of many methodologies, it is our recommended methodology.

* Review the [Required / Recommended Skills](http://blt.readthedocs.io/en/latest/readme/skills) for working with a BLT project
* Ensure that your computer meets the minimum installation requirements (and then install the required applications). See the [System Requirements](http://blt.readthedocs.io/en/latest/INSTALL/#system-requirements).
* Request access to the Creighton Software Bitbucket team (if needed)
* Request access to the Acquia Cloud Environment for your project (if needed)
* Setup a SSH key that can be used for Bitbucket and the Acquia Cloud (you CAN use the same key)
  * [Set up Bitbucket SSH Keys](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
  * [Set up Acquia Cloud SSH Keys](https://docs.acquia.com/acquia-cloud/ssh/generate)

## Virtual Machine

* Check VirtualBox to ensure that you do not already have a VM named local.creighton.com - if you do, delete it. When prompted, select 'remove all files'.
* Check your hosts file to ensure that you do not already have entries for local.creighton.com - if you do, delete them.

## Get Code

```command-line
~ git clone git@bitbucket.org:creighton-software/drupal8_cu_acsf.git
```

* To ensure that upstream changes to the parent repository may be tracked, add the upstream locally as well.

```command-line
~ git remote add upstream git@bitbucket.org:creighton-software/drupal8_cu_acsf.git
```

## Local Environment Setup

BLT requires a of local environment that implements a LAMP stack. While out of the box templates are provided for Drupal VM, if you prefer you can use another tool such as Docker, Docksal, Lando, (other) Vagrant, or your own custom LAMP stack. BLT works with any local environment, however support is limited for these solutions.

For instructions on setting up Drupal VM, read our documentation [here](http://blt.readthedocs.io/en/9.x/readme/local-development/#using-drupal-vm-for-blt-generated-projects).

* Create a `composer.required.json` in the blt directory and paste in the following:

```json
{
  "repositories": {
    "drupal": {
      "type": "composer",
      "url": "https://packages.drupal.org/8"
    }
  },
  "require": {
    "drupal/core": "^8.5.0",
    "drupal/config_split": "^1.0.0"
  },
  "require-dev": {
    "behat/behat": ">=3.1 <3.4",
    "behat/mink": "~1.7",
    "behat/mink-selenium2-driver": "^1.3.1",
    "bex/behat-screenshot": "^1.2",
    "drupal/drupal-extension": "~3.2",
    "drupal-composer/drupal-scaffold": "^2.1.0",
    "jarnaiz/behat-junit-formatter": "^1.3.2",
    "se/selenium-server-standalone": "^2.53",
    "jakoch/phantomjs-installer":   "2.1.1-p07",
    "dmore/behat-chrome-extension": "^1.0.0",
    "mikey179/vfsStream": "~1.2",
    "sensiolabs-de/deprecation-detector": "dev-master"
  },
  "autoload-dev": {
    "psr-4": {
      "Drupal\\Tests\\PHPUnit\\": "tests/phpunit/src/"
    }
  },
  "autoload": {
    "psr-4": {
      "Acquia\\Blt\\Custom\\": "src/"
    }
  },
  "extra": {
    "composer-exit-on-patch-failure": true,
    "drupal-scaffold": {
      "initial": {
        "sites/default/default.services.yml": "sites/default/services.yml",
        "sites/default/default.settings.php": "sites/default/settings.php"
      }
    },
    "enable-patching": true,
    "patches": {
      "drupal/core": {
        "Clear Twig caches on deploys": "https://www.drupal.org/files/issues/2752961-130.patch"
      }
    }
  },
  "scripts": {
    "blt-alias": "blt blt:init:shell-alias -y --ansi",
    "drupal-scaffold": "DrupalComposer\\DrupalScaffold\\Plugin::scaffold",
    "nuke": [
      "rm -rf vendor composer.lock docroot/core docroot/modules/contrib docroot/profiles/contrib docroot/themes/contrib",
      "@composer clearcache --ansi",
      "@composer install --ansi"
    ],
    "install-phantomjs": [
      "rm -rf vendor/bin/phantomjs",
      "PhantomInstaller\\Installer::installPhantomJS"
    ]
  }
}

```

* Create a `composer.suggested.json` in the blt directory and paste in the following:

```json
{
  "repositories": {
    "asset-packagist": {
      "type": "composer",
      "url": "https://asset-packagist.org"
    }
  },
  "require": {
    "acquia/lightning": "^3.1.0",
    "drupal/acquia_connector": "^1.5.0",
    "drupal/acquia_purge": "^1.0-beta3",
    "drupal/cog": "^1.0.0",
    "drupal/devel": "^1.0.0",
    "drupal/qa_accounts": "^1.0.0-alpha1",
    "drupal/memcache": "^2.0-alpha5",
    "drupal/seckit": "^1.0.0-alpha2",
    "drupal/security_review": "*",
    "drupal/shield": "^1.0.0",
    "drupal/features": "^3.7.0"
  },
  "extra": {
    "patches": {
    }
  }
}

```

* Install Composer Dependencies (warning: this can take some time based on internet speeds)

```command-line
~ composer install
```

* Create Vagrantfile

```command-line
~ touch Vagrantfile
```

* Create a file in the blt directory named `local.blt.yml`

```command-line
~ blt vm
```

  a) Drupal VM is not currently installed. Install it now? (y/n) `y`

  b) Which base box would you like to use? `0`

  c) Do you want to boot Drupal VM? (y/n) `y`

  d) creighton: Pruning invalid NFS exports. Administrator privileges will be required...

```command-line
~ vagrant ssh
~ blt setup
```

  a) You are about to DROP all tables in your 'drupal' database. Do you want to continue? (yes/no) [yes]: `yes`

* Access the site and do necessary work at local.creighton.com by running

```command-line
~ drush uli
```

## Further Setup

BLT 9 and Drush 9 require all blt and drush commands to be executed inside of the VM. Because of this requirement, the VM must have SSH access to Acquia.

```command-line
~ cd ~/.ssh
~ ssh-keygen -b 4096
```

* The public key needs to be added to your Acquia Cloud account. More detail can be found about that [here](https://docs.acquia.com/acquia-cloud/ssh/generate).

* Install Drush Launcher, using the instructions in [this](https://github.com/drush-ops/drush-launcher) Github repository.

## Additional Documentation

Additional [BLT documentation](http://blt.readthedocs.io) may be useful. You may also access a list of BLT commands by running

```command-line
~ blt
```

Note the following properties of this project:

* Primary development branch: master - deploys to dev environment in ACSF
  * dev branching - named branch for each developer
  * feature branching - branching per feature
* Local environment: local
* Local site URL: local.creighton.com

## Working With a BLT Project

BLT projects are designed to instill software development best practices (including git workflows).

Our BLT Developer documentation includes an [example workflow](http://blt.readthedocs.io/en/latest/readme/dev-workflow/#workflow-example-local-development).

### Important Configuration Files

BLT uses a number of configuration (.yml or .json) files to define and customize behaviors. Some examples of these are:

* `blt/blt.yml` (formerly `blt/project.yml` prior to BLT 9.x)
* `blt/local.blt.yml`
* `box/config.yml` (if using Drupal VM)
* `drush/sites` (contains Drush aliases for this project)
* `composer.json` (includes required components, including Drupal Modules, for this project)

## Resources

* [Bitbucket](https://bitbucket.org/creighton-software/drupal8_cu_acsf/)
* [Acquia Cloud](https://cloud.acquia.com)
* [Acquia Cloud Sitefactory](https://www.creighton.acsitefactory.com/)

## Platform Documentation

* [Shell Scripts](https://github.com/cu-webteam/d8-platform/blob/master/ShellScripts.md)

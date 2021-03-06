# Recipes

Deployer has a set of predefined tasks called _recipes_.

Recipes can be included to your `deploy.php` file like this:

~~~ php
require 'recipe/common.php';
~~~

### Common Recipe

The common recipe is used by all other recipes. It creates the following directory structure:

~~~
|-- current → /var/www/site.com/releases/20140812131123
|-- releases
|   `-- 20140812131123
|   `-- 20140809150234
|   `-- 20140801145678
`-- shared
   |-- web
   |   `-- uploads
   |-- log
   `-- config
       `-- databases.yml
~~~

~~~
deploy:prepare
~~~

This task prepare server for deploy, creates a `releases` and a `shared` directory.

* `releases` - contains your project releases.
* `shared` - contains shared/common files and directories between releases (logs, shared data, etc.).

~~~
deploy:update_code
~~~

Update code from the configured repository and puts it into the directory of the upcoming release.

Use the `set` function to specify which repository to use:

~~~ php
set('repository', 'git@github.com:user/project.git');
~~~

**Note:** The remote server has to be able to clone your repository.

~~~
deploy:shared
~~~

Creates a symlink to the shared files and directories. Use `set` to define them.

~~~ php
set('shared_dirs', ['app/logs']);

set('shared_files', ['app/config/parameters.yml']);
~~~

~~~
deploy:writable_dirs
~~~

Creates writable directories.

~~~ php
set('writable_dirs', ['app/cache', 'app/logs']);
~~~

~~~
deploy:vendors
~~~

Installs vendors with composer.

~~~
deploy:symlink
~~~

Creates a symlink named `current` which points to the lastest release.

~~~
cleanup
~~~

Removes old releases and keeps the last 3. To change the number of kept releases:

~~~ php
set('keep_releases', 5);
~~~

~~~
rollback
~~~

Rollback to the previous release. If only one release is available, nothing will be done.

### Composer Recipe

~~~ php
require 'recipe/composer.php'
~~~

The composer recipe is a simple recipe suitable for simple projects which uses composer.

It consists of the following tasks:

* deploy:start
* deploy:prepare
* deploy:update_code
* deploy:vendors
* deploy:symlink
* cleanup
* deploy:end

### Symfony Recipe

~~~ php
require 'recipe/symfony.php'
~~~

This recipe is specifically for deploying Symfony2 projects.

It consists of the following tasks:

* deploy:start
* deploy:prepare
* deploy:update_code
* deploy:shared
* deploy:writeable_dirs
* deploy:assets
* deploy:vendors
* deploy:assetic:dump
* database:migrate
* deploy:cache:warmup
* deploy:symlink
* cleanup
* deploy:end

The default parameters of this recipe are:

~~~ php
// Symfony Environment
set('env', 'prod');

// Symfony shared dirs
set('shared_dirs', ['app/logs']);

// Symfony shared files
set('shared_files', ['app/config/parameters.yml']);

// Symfony writeable dirs
set('writeable_dirs', ['app/cache', 'app/logs']);

// Assets
set('assets', ['web/css', 'web/images', 'web/js']);

// In "-v" verbose mode you will be asked to migrate
set('auto_migrate', false);
~~~

# Configuration

To setup configuration param use `set` function, and to get it inside task use `get` function.

```php
set('param', 'value');

task('deploy', function () {
    $param = get('param');
});
```

Each param can be overridden for each host:
  
```php
host(...)
    ->set('param', 'new value');
```

Configuration parameters also can be specified as callback function, which will be executed on remote host on first `get` call:

```php
set('current_path', function () {
    return run('pwd');
});
```

You can use param's values inside `run` calls with `{{ }}`, instead of doing this:

```php
run('cd ' . get('release_path') . ' && command');
```

You can do it:

```php
run('cd {{release_path}} && command');
```

Common recipe comes with a few predefined config params listed below. 
To get list of available params run:

~~~sh
dep config:dump
~~~

### deploy_path

Where to deploy application on remote host.

### ssh_multiplexing

Use [ssh multiplexing](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Multiplexing) for speedup native ssh client.

```php
set('ssh_multiplexing', true);
```

### default_stage

If hosts declaration have stages, this option allow you to select default stage to deploy with `dep deploy`.

```php
set('default_stage', 'prod');

host(...)
    ->stage('prod');
```

### keep_releases

Number of releases to keep. `-1` for unlimited releases.

### repository

Git repository of application.

To use a private repository it needs to generate a SSH-key on your host and add to the repository
as a Deploy Key (a.k.a. Access Key). This key allows your host to pull out the code. Or use can use agent forwarding. 

Note that at the first time host can ask to add host in `known_hosts` file. The easiest way to do it is
running `git clone <repo>` on your host and saying `yes`.

### git_tty

Allocate TTY for `git clone` command. This allow you to enter passphrase for keys.

### branch

Branch to deploy.

If you want to deploy a specific tag or a revision, you can use `--tag` and `--revision` options while running `dep deploy`. F.e.

```bash
dep deploy --tag="v0.1"
dep deploy --revision="5daefb59edbaa75"
```

Note that `tag` has higher priority than `branch` and lower than `revision`.

### shared_dirs

List of shared dirs.

```php
set('shared_dirs', [
    'logs',
    'var',
    ...
]);
```

### shared_files

List of shared files.

### copy_dirs

List of files to copy between release.

### writable_dirs

List of dirs which must be writable for web server.

### writable_mode

Writable mode

* `acl` (*default*) use `setfacl` for changing ACL of dirs.
* `chmod` use unix `chmod` command,
* `chown` use unix `chown` command,
* `chgrp` use unix `chgrp` command,

### writable_use_sudo

Use or not `sudo` with writable command. Default to `false`.

### writable_chmod_mode

Mode for using then `writable_mode` in `chmod`. Default: `0755`.

### writable_chmod_recursive

Whether to set `chmod` on dirs recursively or not. Default: `true`.

### http_user

User of web server. If this parameter does not configured, deployer try to detect it from process list. 

### clear_paths

List of paths which need to be deleted in release after updating code. 

### clear_use_sudo

Use or ro not `sudo` with clear_paths. Default to `false`.

### cleanup_use_sudo

Use or ro not `sudo` with `cleanup` task. Default to `false`.

### use_relative_symlink

Use or not relative symlinks. By default deployer will detect if system supports relative symlinks and use it.

### use_atomic_symlink

Use or not atomic symlinks. By default deployer will detect if system supports atomic symlinks and use it.

### composer_action

Composer action. Default `install`.

### composer_options

Options for composer.

### env_vars

Environment variables.

Read more about [task definitions](tasks.md).

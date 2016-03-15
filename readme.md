# PHPloy
**Version 4.0.0-beta**

PHPloy is an incremental Git FTP and SFTP deployment tool. By keeping track of the state of the remote server(s) it deploys only the files that were committed since the last deployment. PHPloy supports submodules, sub-submodules, deploying to multiple servers and rollbacks. PHPloy requires **PHP 5.4+** and **Git 1.8+**.

## How it works

PHPloy stores a file called `.revision` on your server. This file contains the hash of the commit that you have deployed to that server. When you run phploy, it downloads that file and compares the commit reference in it with the commit you are trying to deploy to find out which files to upload. PHPloy also stores a `.revision` file for each submodule in your repository.

## Install 

You can install PHPloy globally, in your `/usr/local/bin` directory or, locally, in your project directory. Rename `phploy.phar` to `phploy` for ease of use.

1. **Globally:** Move `phploy` into `/usr/local/bin`. Make it executable by running `sudo chmod +x phploy`.
2. **Locally** Move `phploy` into your project directory. 

## Usage 
*When using PHPloy locally, procceed the command with `php `*

1. Run `(php) phploy --init` in the terminal to create the `phploy.ini` file or create one manually.
2. Run `(php) phploy` in terminal to deploy.

Windows Users: [Installing PHPloy globally on Windows](https://github.com/banago/PHPloy/issues/214)

## phploy.ini

The `phploy.ini` file holds your project configuration. It must be in the root directory of your project. Check the sample below for all availalble options:

```ini
; This is a sample deploy.ini file. You can specify as many
; servers as you need and use normal or quickmode configuration.
;
; NOTE: If a value in the .ini file contains any non-alphanumeric 
; characters it needs to be enclosed in double-quotes (").

[staging]
    scheme = sftp
    user = example
    ; When connecting via SFTP, you can opt for password-based authentication:
    pass = password
    ; Or private key-based authentication:
    privkey = 'path/to/or/contents/of/privatekey'
    host = staging-example.com
    path = /path/to/installation
    port = 22
    ; You can speify a branch to deploy from
    branch = develop
    ; Files that should be ignored and not uploaded to your server, but still tracked in your repository
    exclude[] = 'src/*.scss'
    exclude[] = '*.ini'
    ; Files that are ignored by Git, but you want to send the the server
    include[] = 'js/scripts.min.js'
    include[] = 'js/style.min.css'
    include[] = 'diretory-name/'
    purge[] = "cache/"
    pre-deploy[] = "wget http://staging-example.com/pre-deploy/test.php --spider --quiet"
    post-deploy[] = "wget http://staging-example.com/post-deploy/test.php --spider --quiet"
    
[production]
    quickmode = ftp://example:password@production-example.com:21/path/to/installation
    passive = true
    ; You can speify a branch to deploy from
    branch = master
    ; Files that should be ignored and not uploaded to your server, but still tracked in your repository
    exclude[] = 'libs/*'
    exclude[] = 'config/*'
    exclude[] = 'src/*.scss'
    ; Files that are ignored by Git, but you want to send the the server
    include[] = 'js/scripts.min.js'
    include[] = 'js/style.min.css'
    include[] = 'diretory-name/'
    purge[] = "cache/" 
    pre-deploy[] = "wget http://staging-example.com/pre-deploy/test.php --spider --quiet"
    post-deploy[] = "wget http://staging-example.com/post-deploy/test.php --spider --quiet"
```

If your password is missing in the `phploy.ini` file, PHPloy will interactively ask you for your password.

## Multiple servers

PHPloy allows you to configure multiple servers in the deploy file and deploy to any of them with ease. 

By default PHPloy will deploy to *ALL* specified servers.  Alternatively, if an entry named 'default' exists in your server configuration, PHPloy will default to that server configuration. To specify one single server, run:

    phploy -s servername

or:

    phploy --server servername
    
`servername` stands for the name you have given to the server in the `phploy.ini` configuration file.

If you have a 'default' server configured, you can specify to deploy to all configured servers by running:

    phploy --all

## Rollbacks

**Warning: the --rollback option does not currently update your submodules correctly.**

PHPloy allows you to roll back to an earlier version when you need to. Rolling back is very easy. 

To roll back to the previous commit, you just run:

    phploy --rollback

To roll back to whatever commit you want, you run:

    phploy --rollback="commit-hash-goes-here"

When you run a rollback, the files in your working copy will revert **temporarily** to the version of the rollback you are deploying. When the deployment has finished, everything will go back as it was.

Note that there is not a short version of `--rollback`.


## Listing changed files

PHPloy allows you to see what files are going to be uploaded/deleted before you actually push them. Just run: 

    phploy -l

Or:

    phploy --list

## Updating or "syncing" the remote revision

If you want to update the `.revision` file on the server to match your current local revision, run:

    phploy --sync

If you want to set it to a previous commit revision, just specify the revision like this:

    phploy --sync="your-revision-hash-here"

## Submodules

Submodules are supported, but are turned off by default since you don't expect them to change very often and you only update them once in a while. To run a deployment with submodule scanning, add the `--submodules` paramenter to the command:

    phploy --submodules
    
## Purging

In many cases, we need to purge the contents of a directory after a deployment. This can be achieved by specifing the directories in `phploy.ini` like this:

    ; relative to the deployment path
    purge[] = "cache/"
    
## Hooks

PHPloy allows you to execute commands before and after the deployment. For example you can use `wget`  call a script on my server to execute a `composer update`.

    ; To execute before deployment
    pre-deploy[] = "wget http://staging-example.com/pre-deploy/test.php --spider --quiet"
    ; To execute after deployment
    post-deploy[] = "wget http://staging-example.com/post-deploy/test.php --spider --quiet"

## Contribute

Contributions are very welcome; PHPloy is great because of the contributors. Please check out the [issues](https://github.com/banago/PHPloy/issues). 

## Credits

 * [Baki Goxhaj](https://twitter.com/banago)
 * [Contributors](https://github.com/banago/PHPloy/graphs/contributors?type=a)

## Version history

Please check [release history](https://github.com/banago/PHPloy/releases) for details.

## License

PHPloy is lisenced under the MIT License (MIT).

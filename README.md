Description
===========

Heat template to deploy a single server running a PHP app under apache


Instructions
===========

#### Getting Started
If you're new to PHP, the [Getting
Started](http://www.php.net/manual/en/getting-started.php) page can walk you
through the basics of PHP and it's uses.

#### Logging in via SSH
The private key provided in the passwords section can be used to login as
root via SSH. We have an article on how to use these keys with [Mac OS X and
Linux](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-linuxmac)
as well as [Windows using
PuTTY](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-windows).

#### Deploying Your Code
If your deployment has multiple application servers, deployment tools such as
Capistrano, Phing, or even git can simplify the deployment process. It's best
to evaluate all options independently to determine which tool works best for
your use case. There are other methods for deployment including using
configuration and infrastructure management tools like
[Chef](http://docs.opscode.com/resource_deploy.html#deploy-strategies).

#### Details of Your Setup
This deployment was stood up using
[chef-solo](http://docs.opscode.com/chef_solo.html). Once the deployment is
up, chef will not run again, so it is safe to modify configurations.

[PHP](http://www.php.net/) was installed using the packaged version for your
operating system. Newer versions of PHP will be available with newer versions
of the operating system. We recommend using the packaged version as opposed
to a source based installation for easier long term managmement, patching,
and supportability.

All web content will be served by [Apache](http://httpd.apache.org/).  The
configuration can be found in /etc/apache2/sites-enabled. The name of the
configuration file will be the same as the name of the site provided as a
part of this deployment. Any changes made to this configuration will require
a restart of Apache. By default, the Document Root for your site will be
/var/www/vhosts/application/.

[Memcached](http://memcached.org/) is installed to handle object caching. By
default, Memcached is configured to listen on localhost, port 11211.
Memcached is most commonly used to cache object information so that they can
be called from memory as opposed to querying a backend database. The
configuration can be found in /etc/memcached.conf. Any changes made to the
configuration file will require a the memcached service to be restarted.

[MySQL](http://www.mysql.com/) has been installed on a Cloud Database
instance.

#### Adding PHP Modules or Extensions
There are several options for adding support to PHP. We recommend leveraging
as many packaged modules as possible. Use the system package manager to
search for an install modules. Here's an example of how to search for
availalbe modules of PHP 5 on Ubuntu:
```bash
root@app01:~# apt-cache search php5-
php5-cgi - server-side, HTML-embedded scripting language (CGI binary)
php5-cli - command-line interpreter for the php5 scripting language
php5-common - Common files for packages built from the php5 source
php5-curl - CURL module for php5
php5-dbg - Debug symbols for PHP5
php5-dev - Files for PHP5 module development
php5-gd - GD module for php5
php5-gmp - GMP module for php5
php5-ldap - LDAP module for php5
php5-mysql - MySQL module for php5
...
```
Once you find the appropriate package, the package manager can be used to
handle the installation. Here's an example on how we would install the
php5-gd package on Ubuntu:
```bash
apt-get install -y php5-gd
```
If you're looking to install something that is not found in the default
repositories, you can use [PECL](http://pecl.php.net/),
[PEAR](http://pear.php.net/), or
[phpize](http://php.net/manual/en/install.pecl.phpize.php) to complete the
installation. Follow any instructions provided with the application you are
installing for more detail on how to best install the necessary modules or
extensions.

#### Updating PHP
Since the PHP installation was done using the package manager for the
operating system, the update can also be performed by the package manager. If
you're looking for a different major or minor version of PHP, you may want to
consider running a different version of the operating system. Most operating
systems lock in specific major, minor, and release versions, and they only
release updates for bugs or security fixes.


Requirements
============
* A Heat provider that supports the following:
  * OS::Heat::RandomString
  * OS::Heat::SwiftSignal
  * OS::Heat::SwiftSignalHandle
  * OS::Nova::KeyPair
  * OS::Nova::Server
* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.8`:

```bash
pip install python-heatclient
```

We recommend installing the client within a [Python virtual
environment](http://www.virtualenv.org/).

Parameters
==========
Parameters can be replaced with your own values when standing up a stack. Use
the `-P` flag to specify a custom parameter.

* `server_hostname`: Server Name (Default: php)
* `image`: Required: Server image used for all servers that are created as a part of
this deployment.
 (Default: Ubuntu 14.04 LTS (Trusty Tahr) (PVHVM))
* `flavor`: Required: Rackspace Cloud Server flavor to use. The size is based on the
amount of RAM for the provisioned server.
 (Default: 4 GB General Purpose v1)
* `revision`: Optional: Git Branch/Ref to deploy. Default: HEAD
 (Default: HEAD)
* `packages`: Optional: Additional system packages to install. Must be formatted as
as a python list: ['package1', 'package2', 'etc']
 (Default: '')
* `repo`: Optional: URL to your git repository. Use the https syntax for public
repositories, use git@ syntax for private repositories.
 (Default: '')
* `url`: URL for site (Default: example.com)
* `deploy_key`: Optional: If you specified a private repository, provide your private
deploy key here.
 (Default: '')
* `destination`: Path to setup your application on your servers. (Default: /var/www/vhosts/application)
* `public`: The public facing directory of your application relative to the
destination.
 (Default: /)
* `http_port`: HTTP Port (Default: 80)
* `https_port`: HTTPS Port (Default: 443)
* `memcached_size`: Memcached memory size limit (Default: 128)

Outputs
=======
Once a stack comes online, use `heat output-list` to see all available outputs.
Use `heat output-show <OUTPUT NAME>` to get the value of a specific output.

* `private_key`: SSH Private Key 
* `server_ip`: Server IP 
* `mysql_root_password`: MySQL Root Password 

For multi-line values, the response will come in an escaped form. To get rid of
the escapes, use `echo -e '<STRING>' > file.txt`. For vim users, a substitution
can be done within a file using `%s/\\n/\r/g`.

Ban IPs from nginx with fail2ban with redis as the broker

TODO: does this correctly account for the X-Forwaded-For header?

What?
-----

This is a proof of concept of a setup where fail2ban updates a list of IPs to
redis and nginx uses that list to block requests.

redis.conf includes the fail2ban action for updating the IPs in redis.
By default, it stores them in  a redis set named fail2ban-*my-jail-name*.

jail.local contains an example configuration of a jail that uses the action.
Alter as you see fit.

nginx.conf contains an nginx configuration that uses lua scripting to block
requests based on the redis set of IPs. Take out the relevant parts of the
configuration.


Required packages
-----------------

- fail2ban (tested with 0.8, should work with 0.9 too)
- redis-server (and redis-cli, should be intalled with redis-server)
- nginx with lua module and lua-resty-redis package
  - See http://wiki.nginx.org/HttpLuaModule#Installation
  - The ngx_openresty bundle should install everything required
  - Otherwise, refer to the page for installation instructions and install
    lua-resty-redis as per this page:
    https://github.com/openresty/lua-resty-redis#installation

Installation
------------

    # link redis action
    $ ln -s /path/to/redis.conf /etc/fail2ban/action.d/redis.conf
    # copy relevant parts of the jail config or symlink the example:
    $ ln -s /path/to/jail.local /etc/fail2ban/jail.local  # example only !!
    # copy relevant parts of the nginx config or symlink the example:
    $ ln -s /path/to/nginx.conf /etc/nginx/sites-enabled/my-site  # example only !!
    # restart processess
    $ sudo service fail2ban restart
    $ sudo service nginx restart

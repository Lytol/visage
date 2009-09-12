visage
======

Visage is a web interface for viewing `collectd` statistics.

It also provides a JSON interface onto `collectd`'s RRD data. giving you an easy
way to mash up the data.

Installing
----------

Freeze in dependencies:

    $ rake deps

This will pull in RubyRRDtool, which requires the rrdtool headers to build a C
extension. On Ubuntu these are in the `librrd2-dev` package.

Configuring
-----------

Config lives in several files under `config/`. 

 * `profiles.yaml` - graphs Visage is to display
 * `plugin-colors.yaml` - colors for specific plugins/plugin instances
 * `fallback-colors.yaml` - ordered list of fallback colors
 * `init.rd` - bootstrapping code, specifies collectd's RRD directory

`profiles.yaml` isn't setup by default, but you can copy `profiles.yaml.sample`
across and edit to taste. The plugins are in the format of 
`plugin`/`plugin-instance`, with `plugins-instance` being optional. 

If you don't specify a `plugin-instance` Visage will attempt to graph all plugin
instances under the specified `plugin`, e.g. `cpu-0` will display `cpu-idle`, 
`cpu-interrupt`, `cpu-nice`, etc, whereas `cpu-0/cpu-wait` will only show 
`cpu-wait`. 

You should be able to deduce the config format from the existing file (it's
simple nested key-value data).

Make sure collectd's RRD directory is readable by whatever user the web server
is running as. You can specify where collectd's rrd directory is in `init.rb`,
with the `c['rrddir']` key.

Developing
----------

For development: 

    $ gem install shotgun
    $ shotgun sinatra-collectd.rb

Deploying
---------

With Passenger, create an Apache vhost with the `DocumentRoot` set to the 
`public/` directory of where you have deployed the checked out code, e.g.

    <VirtualHost *>
      ServerName visage.example.org
      ServerAdmin contact@visage.example.org
    
      DocumentRoot /srv/www/visage.example.org/root/public/
    
      <Directory "/srv/www/visage.example.org/root/public/">
         Options FollowSymLinks Indexes
         AllowOverride None
         Order allow,deny
         Allow from all 
       </Directory>
    
       ErrorLog /srv/www/visage.example.org/log/apache_errors_log
       CustomLog /srv/www/visage.example.org/log/apache_app_log combined
    
    </VirtualHost>

This assumes you have a checkout of the code at `/srv/www/visage.example.org/root`.

If you don't want to use Apache + Passenger, you can install the `thin` or 
`mongrel` gems and run up a web server yourself. 


Testing 
-------

Run all cucumber features: 

    $ rake cucumber 

Specific features: 

    $ bin/cucumber --require features/ features/something.feature

TODO
----

 * create proper mootools class - DONE
 * switch to g.raphael - DONE
 * config file - DONE
 * data profiles - DONE
 * handle single plugin instance in graphing code - DONE
 * specify data url in graphing code - DONE
 * generate holders for graph/labels/time selector - DONE
 * clean up routes - DONE
 * smart colour selection (CPU-1 = CPU) - DONE
 * title attributes - DONE
 * split profiles + colors => plugins into separate files - DONE
 * fix key labels - DONE
 * axis labels (with human readable times)
 * detailed point-in-time data on hover
 * embedded html generator
 * zoom + dynamic resize - DONE
 * combine graphs from different hosts
 * comment on time periods
 * view list of comments

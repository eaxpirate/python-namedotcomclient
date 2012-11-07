Name.com Command Line Client
============================

This script is under active development. Use at your own risk.

This is an extremely basic client written for the name.com api to allow me some
automatic DNS configuration of my cloudservers built at rackspace. This script
is part of my deploy process, however, it is also relatively useful as a stand-
alone client for anyone wishing to manage their name.com hosted zones via CLI.

Requirements
============

Python >= 2.6 (havent tested it on other stuff, but it would probably work)
json
prettytable
httplib2
getopt
ConfigParser

TODO
====

  * setuptools
  * Create an actual library
  * Error handling and input sanitization
  * replace getopt
  * Determine the timeout for the session keys and decide on whether or not to -
    cache
  * Add a ''quiet'' flag for scripting

Installation
============

Right now there is just a script and a config file - the config file should be:

    ** ~/.namedotcom.conf**

and contain the following:

    [namedotcomconfig]
    username = web_interface_username
    api_token = api_token,_obtained_by_asking_name.com_support_for_one
    api_auth_url = https://api.name.com/api/login

Make sure you change the api_auth_url or specify it when running interactively
to switch between prod/dev.

  * Copy script tp $PATH
  * Copy namedotcom.conf.sample ~/.namedotcom.conf


Usage
=====

Name.com DNS Manager

Usage: ./namedotcom [--username username] [--api_token token] <command>

--username - the username you use to login to the web interface
--api_token - the token you were given by support when you asked for it
<command> - can be any one of the following:
	--list-domains                              - list all of your domains
	--list-records <domain>                     - list all records in domain
	--create-record <name>,<domain>,<ip>,<type> - create subdomain for domain
	--delete-record <domain>,<record id>        - Delete a record


Example Output
==============

    $ namedotcom --list-domains
    +---------+
    |  Domain |
    +---------+
    | niko.im |
    | usod.us |
    +---------+
    $ namedotcom --list-records niko.im
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    |       Created       |         Name         |                   IP                   | TTL | Record ID | Type |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    | 2012-10-19 09:58:45 |      an.niko.im      |            174.136.105.210             | 300 | 230631053 |  A   |
    | 2012-10-19 09:59:12 |      an.niko.im      |            174.136.105.211             | 300 | 230631056 |  A   |
    | 2012-10-19 09:59:25 |      an.niko.im      |            174.136.105.212             | 300 | 230631059 |  A   |
    | 2012-10-19 09:59:43 |      an.niko.im      |            174.136.105.213             | 300 | 230631060 |  A   |
    | 2012-10-19 09:59:55 |      an.niko.im      |            174.136.105.214             | 300 | 230631061 |  A   |
    | 2012-10-08 23:42:57 |     blog.niko.im     |             198.61.196.97              | 300 | 230414960 |  A   |
    | 2012-10-08 23:43:15 |     blog.niko.im     | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230414981 | AAAA |
    | 2012-10-25 15:56:14 | drgirlfriend.niko.im |             64.49.226.222              | 300 | 230715380 |  A   |
    | 2012-10-25 15:56:26 | drgirlfriend.niko.im | 2001:4800:7810:512:bd86:1d06:ff04:7ed9 | 300 | 230715381 | AAAA |
    | 2012-10-09 22:22:14 |       niko.im        |             198.61.196.97              | 300 | 230432310 |  A   |
    | 2012-10-09 22:22:22 |       niko.im        | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230432311 | AAAA |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    $ namedotcom --create-record example,niko.im,1.2.3.4,A
    +--------------------+
    |       Result       |
    +--------------------+
    | Command Successful |
    +--------------------+
    $ namedotcom --list-records niko.im | egrep "^\+|Created|example"
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    |       Created       |         Name         |                   IP                   | TTL | Record ID | Type |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    | 2012-11-07 03:22:49 |   example.niko.im    |                1.2.3.4                 | 300 | 230964610 |  A   |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    $ namedotcom --create-record example,niko.im,2001:4800:780e:510:bd86:1d06:ff04:6105,AAAA
    +--------------------+
    |       Result       |
    +--------------------+
    | Command Successful |
    +--------------------+
    $ namedotcom --list-records niko.im | egrep "^\+|Created|example"
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    |       Created       |         Name         |                   IP                   | TTL | Record ID | Type |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    | 2012-11-07 03:22:49 |   example.niko.im    |                1.2.3.4                 | 300 | 230964610 |  A   |
    | 2012-11-07 03:24:11 |   example.niko.im    | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230964653 | AAAA |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    $ namedotcom --list-records niko.im | egrep "^\+|Created|example"
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    |       Created       |         Name         |                   IP                   | TTL | Record ID | Type |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    | 2012-11-07 03:22:49 |   example.niko.im    |                1.2.3.4                 | 300 | 230964610 |  A   |
    | 2012-11-07 03:24:11 |   example.niko.im    | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230964653 | AAAA |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    $ namedotcom --delete-record niko.im,230964610
    +--------------------+
    |       Result       |
    +--------------------+
    | Command Successful |
    +--------------------+
    $ namedotcom --delete-record niko.im,230964653
    +--------------------+
    |       Result       |
    +--------------------+
    | Command Successful |
    +--------------------+
    $ namedotcom --list-records niko.im
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    |       Created       |         Name         |                   IP                   | TTL | Record ID | Type |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+
    | 2012-10-19 09:58:45 |      an.niko.im      |            174.136.105.210             | 300 | 230631053 |  A   |
    | 2012-10-19 09:59:12 |      an.niko.im      |            174.136.105.211             | 300 | 230631056 |  A   |
    | 2012-10-19 09:59:25 |      an.niko.im      |            174.136.105.212             | 300 | 230631059 |  A   |
    | 2012-10-19 09:59:43 |      an.niko.im      |            174.136.105.213             | 300 | 230631060 |  A   |
    | 2012-10-19 09:59:55 |      an.niko.im      |            174.136.105.214             | 300 | 230631061 |  A   |
    | 2012-10-08 23:42:57 |     blog.niko.im     |             198.61.196.97              | 300 | 230414960 |  A   |
    | 2012-10-08 23:43:15 |     blog.niko.im     | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230414981 | AAAA |
    | 2012-10-25 15:56:14 | drgirlfriend.niko.im |             64.49.226.222              | 300 | 230715380 |  A   |
    | 2012-10-25 15:56:26 | drgirlfriend.niko.im | 2001:4800:7810:512:bd86:1d06:ff04:7ed9 | 300 | 230715381 | AAAA |
    | 2012-10-09 22:22:14 |       niko.im        |             198.61.196.97              | 300 | 230432310 |  A   |
    | 2012-10-09 22:22:22 |       niko.im        | 2001:4800:780e:510:bd86:1d06:ff04:6105 | 300 | 230432311 | AAAA |
    +---------------------+----------------------+----------------------------------------+-----+-----------+------+


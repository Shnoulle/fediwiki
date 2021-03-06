### [Back to home](/)

# GNU Social Configuration Options 
(To be edited later)

Configuration options
=====================

The main configuration file for StatusNet (excepting configurations for
dependency software) is config.php in your StatusNet directory. If you
edit any other file in the directory, like lib/default.php (where most
of the defaults are defined), you will lose your configuration options
in any upgrade, and you will wish that you had been more careful.

Starting with version 0.9.0, a Web based configuration panel has been
added to StatusNet. The preferred method for changing config options is
to use this panel.

A command-line script, setconfig.php, can be used to set individual
configuration options. It's in the scripts/ directory.

Starting with version 0.7.1, you can put config files in the
/etc/statusnet/ directory on your server, if it exists. Config files
will be included in this order:

* /etc/statusnet/statusnet.php - server-wide config
* /etc/statusnet/<servername>.php - for a virtual host
* /etc/statusnet/<servername>_<pathname>.php - for a path
* INSTALLDIR/config.php - for a particular implementation

Almost all configuration options are made through a two-dimensional
associative array, cleverly named $config. A typical configuration
line will be:

    $config['section']['option'] = value;

For brevity, the following documentation describes each section and
option.

site
----

This section is a catch-all for site-wide variables.

name: the name of your site, like 'YourCompany Microblog'.
server: the server part of your site's URLs, like 'example.net'.
path: The path part of your site's URLs, like 'statusnet' or ''(installed in root).
fancy: whether or not your site uses fancy URLs (see Fancy URLs section above). Default is false.
logfile: full path to a file for StatusNet to save logging information to. You may want to use this if you don't have access to syslog.
logdebug: whether to log additional debug info like backtraces on hard errors. Default false.
locale_path: full path to the directory for locale data. Unless you store all your locale data in one place, you probably don't need to use this.
language: default language for your site. Defaults to US English.
    Note that this is overridden if a user is logged in and has
    selected a different language. It is also overridden if the
    user is NOT logged in, but their browser requests a different
    langauge. Since pretty much everybody's browser requests a
    language, that means that changing this setting has little or
    no effect in practice.
languages: 
    A list of languages supported on your site. Typically you'd
    only change this if you wanted to disable support for one
    or another language:
    "unset($config['site']['languages']['de'])" will disable
    support for German.
theme:
    Theme for your site (see Theme section). Two themes are
    provided by default: 'default' and 'stoica' (the one used by
    Identi.ca). It's appreciated if you don't use the 'stoica' theme
    except as the basis for your own.
email: 
    contact email address for your site. By default, it's extracted
    from your Web server environment; you may want to customize it.
broughtbyurl: 
    Name of an organization or individual who provides the
    service. Each page will include a link to this name in the
    footer. A good way to link to the blog, forum, wiki,
    corporate portal, or whoever is making the service available.
broughtby: 
    text used for the "brought by" link.
timezone: 
    default timezone for message display. Users can set their
    own time zone. Defaults to 'UTC', which is a pretty good default.
closed: 
    If set to 'true', will disallow registration on your site.
    This is a cheap way to restrict accounts to only one
    individual or group; just register the accounts you want on
    the service, *then* set this variable to 'true'.
inviteonly: 
    If set to 'true', will only allow registration if the user
    was invited by an existing user.
private: 
    If set to 'true', anonymous users will be redirected to the
    'login' page. Also, API methods that normally require no
    authentication will require it. Note that this does not turn
    off registration; use 'closed' or 'inviteonly' for the
    behaviour you want.
notice: 
    A plain string that will appear on every page. A good place
    to put introductory information about your service, or info about
    upgrades and outages, or other community info. Any HTML will
    be escaped.
logo: 
    URL of an image file to use as the logo for the site. Overrides
    the logo in the theme, if any.
ssllogo: 
    URL of an image file to use as the logo on SSL pages. If unset,
    theme logo is used instead.
ssl: 
    Whether to use SSL and https:// URLs for some or all pages.
    Possible values are 'always' (use it for all pages), 'never'
    (don't use it for any pages), or 'sometimes' (use it for
    sensitive pages that include passwords like login and registration,
    but not for regular pages). Default to 'never'.
sslproxy: 
    Whether to force GNUsocial to think it is HTTPS when the
    server gives no such information. I.e. when you're using a reverse
    proxy that adds the encryption layer but the webserver that runs PHP
    isn't configured with a key and certificate.
sslserver: 
    use an alternate server name for SSL URLs, like
    'secure.example.org'. You should be careful to set cookie
    parameters correctly so that both the SSL server and the
    "normal" server can access the session cookie and
    preferably other cookies as well.
shorturllength: 
    ignored. See 'url' section below.
dupelimit: 
    minimum time allowed for one person to say the same thing
    twice. Default 60s. Anything lower is considered a user
    or UI error.
textlimit: 
    default max size for texts in the site. Defaults to 0 (no limit).
    Can be fine-tuned for notices, messages, profile bios and group descriptions.

db
--

This section is a reference to the configuration options for
DB_DataObject (see <http://ur1.ca/7xp>). The ones that you may want to
set are listed below for clarity.

database: 
    a DSN (Data Source Name) for your StatusNet database. This is
    in the format 'protocol://username:password@hostname/databasename',
    where 'protocol' is 'mysql' or 'mysqli' (or possibly 'postgresql', if you
    really know what you're doing), 'username' is the username,
    'password' is the password, and etc.
ini_yourdbname: 
    if your database is not named 'statusnet', you'll need
    to set this to point to the location of the
    statusnet.ini file. Note that the real name of your database
    should go in there, not literally 'yourdbname'.
db_driver: You can try changing this to 'MDB2' to use the other driver
    type for DB_DataObject, but note that it breaks the OpenID
    libraries, which only support PEAR::DB.
debug: On a database error, you may get a message saying to set this
    value to 5 to see debug messages in the browser. This breaks
    just about all pages, and will also expose the username and
    password
quote_identifiers: Set this to true if you're using postgresql.
type: either 'mysql' or 'postgresql' (used for some bits of
    database-type-specific SQL in the code). Defaults to mysql.
mirror: you can set this to an array of DSNs, like the above
    'database' value. If it's set, certain read-only actions will
    use a random value out of this array for the database, rather
    than the one in 'database' (actually, 'database' is overwritten).
    You can offload a busy DB server by setting up MySQL replication
    and adding the slaves to this array. Note that if you want some
    requests to go to the 'database' (master) server, you'll need
    to include it in this array, too.
utf8: whether to talk to the database in UTF-8 mode. This is the default
    with new installations, but older sites may want to turn it off
    until they get their databases fixed up. See "UTF-8 database"
    above for details.
schemacheck: when to let plugins check the database schema to add
    tables or update them. Values can be 'runtime' (default)
    or 'script'. 'runtime' can be costly (plugins check the
    schema on every hit, adding potentially several db
    queries, some quite long), but not everyone knows how to
    run a script. If you can, set this to 'script' and run
    scripts/checkschema.php whenever you install or upgrade a
    plugin.

syslog
------

By default, StatusNet sites log error messages to the syslog facility.
(You can override this using the 'logfile' parameter described above).

appname: The name that StatusNet uses to log messages. By default it's
    "statusnet", but if you have more than one installation on the
    server, you may want to change the name for each instance so
    you can track log messages more easily.
priority: level to log at. Currently ignored.
facility: what syslog facility to used. Defaults to LOG_USER, only
    reset if you know what syslog is and have a good reason
    to change it.

queue
-----

You can configure the software to queue time-consuming tasks, like
sending out SMS email or XMPP messages, for off-line processing. See
'Queues and daemons' above for how to set this up.

enabled: Whether to uses queues. Defaults to false.
daemon: Wather to use queuedaemon. Defaults to false, which means
    you'll use OpportunisticQM plugin.
subsystem: Which kind of queueserver to use. Values include "db" for
    our hacked-together database queuing (no other server
    required) and "stomp" for a stomp server.
stomp_server: "broker URI" for stomp server. Something like
    "tcp://hostname:61613". More complicated ones are
    possible; see your stomp server's documentation for
    details.
queue_basename: a root name to use for queues (stomp only). Typically
    something like '/queue/sitename/' makes sense. If running
    multiple instances on the same server, make sure that
    either this setting or $config['site']['nickname'] are
    unique for each site to keep them separate.

stomp_username: username for connecting to the stomp server; defaults
    to null.
stomp_password: password for connecting to the stomp server; defaults
    to null.

stomp_persistent: keep items across queue server restart, if enabled.
    Under ActiveMQ, the server configuration determines if and how
    persistent storage is actually saved.

    If using a message queue server other than ActiveMQ, you may
    need to disable this if it does not support persistence.

stomp_transactions: use transactions to aid in error detection.
    A broken transaction will be seen quickly, allowing a message
    to be redelivered immediately if a daemon crashes.

    If using a message queue server other than ActiveMQ, you may
    need to disable this if it does not support transactions.

stomp_acks: send acknowledgements to aid in flow control.
    An acknowledgement of successful processing tells the server
    we're ready for more and can help keep things moving smoothly.

    This should *not* be turned off when running with ActiveMQ, but
    if using another message queue server that does not support
    acknowledgements you might need to disable this.

softlimit: an absolute or relative "soft memory limit"; daemons will
    restart themselves gracefully when they find they've hit
    this amount of memory usage. Defaults to 90% of PHP's global
    memory_limit setting.

inboxes: delivery of messages to receiver's inboxes can be delayed to
    queue time for best interactive performance on the sender.
    This may however be annoyingly slow when using the DB queues,
    so you can set this to false if it's causing trouble.

breakout: for stomp, individual queues are by default grouped up for
    best scalability. If some need to be run by separate daemons,
    etc they can be manually adjusted here.

        Default will share all queues for all sites within each group.
        Specify as <group>/<queue> or <group>/<queue>/<site>,
        using nickname identifier as site.

        'main/distrib' separate "distrib" queue covering all sites
        'xmpp/xmppout/mysite' separate "xmppout" queue covering just 'mysite'

max_retries: for stomp, drop messages after N failed attempts to process.
    Defaults to 10.

dead_letter_dir: for stomp, optional directory to dump data on failed
    queue processing events after discarding them.

stomp_no_transactions: for stomp, the server does not support transactions,
    so do not try to user them. This is needed for http://www.morbidq.com/.

stomp_no_acks: for stomp, the server does not support acknowledgements.
    so do not try to user them. This is needed for http://www.morbidq.com/.

license
-------

The default license to use for your users notices. The default is the
Creative Commons Attribution 3.0 license, which is probably the right
choice for any public site. Note that some other servers will not
accept notices if you apply a stricter license than this.

type: one of 'cc' (for Creative Commons licenses), 'allrightsreserved'
    (default copyright), or 'private' (for private and confidential
    information).
owner: for 'allrightsreserved' or 'private', an assigned copyright
    holder (for example, an employer for a private site). If
    not specified, will be attributed to 'contributors'.
url: URL of the license, used for links.
title: Title for the license, like 'Creative Commons Attribution 3.0'.
image: A button shown on each page for the license.

mail
----

This is for configuring out-going email. We use PEAR's Mail module,
see: http://pear.php.net/manual/en/package.mail.mail.factory.php

backend: the backend to use for mail, one of 'mail', 'sendmail', and
    'smtp'. Defaults to PEAR's default, 'mail'.
params: if the mail backend requires any parameters, you can provide
    them in an associative array.

nickname
--------

This is for configuring nicknames in the service.

blacklist: an array of strings for usernames that may not be
    registered. A default array exists for strings that are
    used by StatusNet (e.g. 'doc', 'main', 'avatar', 'theme')
    but you may want to add others if you have other software
    installed in a subdirectory of StatusNet or if you just
    don't want certain words used as usernames.
featured: an array of nicknames of 'featured' users of the site.
    Can be useful to draw attention to well-known users, or
    interesting people, or whatever.

avatar
------

For configuring avatar access.

dir: Directory to look for avatar files and to put them into.
    Defaults to avatar subdirectory of install directory; if
    you change it, make sure to change path, too.
path: Path to avatars. Defaults to path for avatar subdirectory,
    but you can change it if you wish. Note that this will
    be included with the avatar server, too.
server: If set, defines another server where avatars are stored in the
    root directory. Note that the 'avatar' subdir still has to be
    writeable. You'd typically use this to split HTTP requests on
    the client to speed up page loading, either with another
    virtual server or with an NFS or SAMBA share. Clients
    typically only make 2 connections to a single server at a
    time <http://ur1.ca/6ih>, so this can parallelize the job.
    Defaults to null.
ssl: Whether to access avatars using HTTPS. Defaults to null, meaning
    to guess based on site-wide SSL settings.

public
------

For configuring the public stream.

localonly: If set to true, only messages posted by users of this
    service (rather than other services, filtered through OStatus)
    are shown in the public stream. Default true.
blacklist: An array of IDs of users to hide from the public stream.
    Useful if you have someone making excessive Twitterfeed posts
    to the site, other kinds of automated posts, testing bots, etc.
autosource: Sources of notices that are from automatic posters, and thus
    should be kept off the public timeline. Default empty.

theme
-----

server: Like avatars, you can speed up page loading by pointing the
    theme file lookup to another server (virtual or real).
    Defaults to NULL, meaning to use the site server.
dir: Directory where theme files are stored. Used to determine
    whether to show parts of a theme file. Defaults to the theme
    subdirectory of the install directory.
path: Path part of theme URLs, before the theme name. Relative to the
    theme server. It may make sense to change this path when upgrading,
    (using version numbers as the path) to make sure that all files are
    reloaded by caching clients or proxies. Defaults to null,
    which means to use the site path + '/theme'.
ssl: Whether to use SSL for theme elements. Default is null, which means
    guess based on site SSL settings.
sslserver: SSL server to use when page is HTTPS-encrypted. If
    unspecified, site ssl server and so on will be used.
sslpath: If sslserver if defined, path to use when page is HTTPS-encrypted.

javascript
----------

server: You can speed up page loading by pointing the
    theme file lookup to another server (virtual or real).
    Defaults to NULL, meaning to use the site server.
path: Path part of Javascript URLs. Defaults to null,
    which means to use the site path + '/js/'.
ssl: Whether to use SSL for JavaScript files. Default is null, which means
    guess based on site SSL settings.
sslserver: SSL server to use when page is HTTPS-encrypted. If
    unspecified, site ssl server and so on will be used.
sslpath: If sslserver if defined, path to use when page is HTTPS-encrypted.
bustframes: If true, all web pages will break out of framesets. If false,
	    can comfortably live in a frame or iframe... probably. Default
	    to true.

xmpp
----

For configuring the XMPP sub-system.

enabled: Whether to accept and send messages by XMPP. Default false.
server: server part of XMPP ID for update user.
port: connection port for clients. Default 5222, which you probably
    shouldn't need to change.
user: username for the client connection. Users will receive messages
    from 'user'@'server'.
resource: a unique identifier for the connection to the server. This
    is actually used as a prefix for each XMPP component in the system.
password: password for the user account.
host: some XMPP domains are served by machines with a different
    hostname. Set this to the correct hostname if that's the
    case with your server.
encryption: Whether to encrypt the connection between StatusNet and the
    XMPP server. Defaults to true, but you can get
    considerably better performance turning it off if you're
    connecting to a server on the same machine or on a
    protected network.
debug: if turned on, this will make the XMPP library blurt out all of
    the incoming and outgoing messages as XML stanzas. Use as a
    last resort, and never turn it on if you don't have queues
    enabled, since it will spit out sensitive data to the browser.
public: an array of JIDs to send _all_ notices to. This is useful for
    participating in third-party search and archiving services.

invite
------

For configuring invites.

enabled: Whether to allow users to send invites. Default true.

tag
---

Miscellaneous tagging stuff.

dropoff: Decay factor for tag listing, in seconds.
    Defaults to exponential decay over ten days; you can twiddle
    with it to try and get better results for your site.

popular
-------

Settings for the "popular" section of the site.

dropoff: Decay factor for popularity listing, in seconds.
    Defaults to exponential decay over ten days; you can twiddle
    with it to try and get better results for your site.

daemon
------

For daemon processes.

piddir: directory that daemon processes should write their PID file
    (process ID) to. Defaults to /var/run/, which is where this
    stuff should usually go on Unix-ish systems.
user: If set, the daemons will try to change their effective user ID
    to this user before running. Probably a good idea, especially if
    you start the daemons as root. Note: user name, like 'daemon',
    not 1001.
group: If set, the daemons will try to change their effective group ID
    to this named group. Again, a name, not a numerical ID.

emailpost
---------

For post-by-email.

enabled: Whether to enable post-by-email. Defaults to true. You will
    also need to set up maildaemon.php.

sms
---

For SMS integration.

enabled: Whether to enable SMS integration. Defaults to true. Queues
    should also be enabled.

integration
-----------

A catch-all for integration with other systems.

taguri: base for tag:// URIs. Defaults to site-server + ',2009'.

inboxes
-------

For notice inboxes.

enabled: No longer used. If you set this to something other than true,
    StatusNet will no longer run.

throttle
--------

For notice-posting throttles.

enabled: Whether to throttle posting. Defaults to false.
count: Each user can make this many posts in 'timespan' seconds. So, if count
    is 100 and timespan is 3600, then there can be only 100 posts
    from a user every hour.
timespan: see 'count'.

profile
-------

Profile management.

biolimit: max character length of bio; 0 means no limit; null means to use
    the site text limit default.
backup: whether users can backup their own profiles. Defaults to true.
restore: whether users can restore their profiles from backup files. Defaults
	 to true.
delete: whether users can delete their own accounts. Defaults to false.
move: whether users can move their accounts to another server. Defaults
      to true.

newuser
-------

Options with new users.

default: nickname of a user account to automatically subscribe new
    users to. Typically this would be system account for e.g.
    service updates or announcements. Users are able to unsub
    if they want. Default is null; no auto subscribe.
welcome: nickname of a user account that sends welcome messages to new
    users. Can be the same as 'default' account, although on
    busy servers it may be a good idea to keep that one just for
    'urgent' messages. Default is null; no message.

If either of these special user accounts are specified, the users should
be created before the configuration is updated.

attachments
-----------

The software lets users upload files with their notices. You can configure
the types of accepted files by mime types and a trio of quota options:
per file, per user (total), per user per month.

We suggest the use of the pecl file_info extension to handle mime type
detection.

supported: an array of mime types you accept to store and distribute,
    like 'image/gif', 'video/mpeg', 'audio/mpeg', etc. Make sure you
    setup your server to properly recognize the types you want to
    support.
uploads: false to disable uploading files with notices (true by default).

For quotas, be sure you've set the upload_max_filesize and post_max_size
in php.ini to be large enough to handle your upload. In httpd.conf
(if you're using apache), check that the LimitRequestBody directive isn't
set too low (it's optional, so it may not be there at all).

process_links: follow redirects and save all available file information
    (mimetype, date, size, oembed, etc.). Defaults to true.
file_quota: maximum size for a single file upload in bytes. A user can send
    any amount of notices with attachments as long as each attachment
    is smaller than file_quota.
user_quota: total size in bytes a user can store on this server. Each user
    can store any number of files as long as their total size does
    not exceed the user_quota.
monthly_quota: total size permitted in the current month. This is the total
    size in bytes that a user can upload each month.
dir: directory accessible to the Web process where uploads should go.
    Defaults to the 'file' subdirectory of the install directory, which
    should be writeable by the Web user.
server: server name to use when creating URLs for uploaded files.
    Defaults to null, meaning to use the default Web server. Using
    a virtual server here can speed up Web performance.
path: URL path, relative to the server, to find files. Defaults to
    main path + '/file/'.
ssl: whether to use HTTPS for file URLs. Defaults to null, meaning to
    guess based on other SSL settings.
sslserver: if specified, this server will be used when creating HTTPS
    URLs. Otherwise, the site SSL server will be used, with /file/ path.
sslpath: if this and the sslserver are specified, this path will be used
    when creating HTTPS URLs. Otherwise, the attachments|path value
    will be used.
show_thumbs: show thumbnails in notice lists for uploaded images, and photos
    and videos linked remotely that provide oEmbed info. Defaults to true.
show_html: show (filtered) text/html attachments (and oEmbed HTML etc.).
    Doesn't affect AJAX calls. Defaults to false.
filename_base: for new files, choose one: 'upload', 'hash'. Defaults to hash.

group
-----

Options for group functionality.

maxaliases: maximum number of aliases a group can have. Default 3. Set
    to 0 or less to prevent aliases in a group.
desclimit: maximum number of characters to allow in group descriptions.
    null (default) means to use the site-wide text limits. 0
    means no limit.
addtag: Whether to add a tag for the group nickname for every group post
	(pre-1.0.x behaviour). Defaults to false.

search
------

Some stuff for search.

type: type of search. Ignored if PostgreSQL or Sphinx are enabled. Can either
    be 'fulltext' or 'like' (default). The former is faster and more efficient
    but requires the lame old MyISAM engine for MySQL. The latter
    will work with InnoDB but could be miserably slow on large
    systems. We'll probably add another type sometime in the future,
    with our own indexing system (maybe like MediaWiki's).

sessions
--------

Session handling.

handle: boolean. Whether we should register our own PHP session-handling
    code (using the database and cache layers if enabled). Defaults to false.
    Setting this to true makes some sense on large or multi-server
    sites, but it probably won't hurt for smaller ones, either.
debug: whether to output debugging info for session storage. Can help
    with weird session bugs, sometimes. Default false.

ping
----

Using the "XML-RPC Ping" method initiated by weblogs.com, the site can
notify third-party servers of updates.

notify: an array of URLs for ping endpoints. Default is the empty
    array (no notification).

notice
------

Configuration options specific to notices.

contentlimit: max length of the plain-text content of a notice.
    Default is null, meaning to use the site-wide text limit.
    0 means no limit.
defaultscope: default scope for notices. If null, the default
	scope depends on site/private. It's 1 if the site is private,
	0 otherwise. Set this value to override.

message
-------

Configuration options specific to messages.

contentlimit: max length of the plain-text content of a message.
    Default is null, meaning to use the site-wide text limit.
    0 means no limit.

logincommand
------------

Configuration options for the login command.

disabled: whether to enable this command. If enabled, users who send
    the text 'login' to the site through any channel will
    receive a link to login to the site automatically in return.
    Possibly useful for users who primarily use an XMPP or SMS
    interface and can't be bothered to remember their site
    password. Note that the security implications of this are
    pretty serious and have not been thoroughly tested. You
    should enable it only after you've convinced yourself that
    it is safe. Default is 'false'.

singleuser
----------

If an installation has only one user, this can simplify a lot of the
interface. It also makes the user's profile the root URL.

enabled: Whether to run in "single user mode". Default false.
nickname: nickname of the single user. If no nickname is specified,
          the site owner account will be used (if present).

robotstxt
---------

We put out a default robots.txt file to guide the processing of
Web crawlers. See http://www.robotstxt.org/ for more information
on the format of this file.

crawldelay: if non-empty, this value is provided as the Crawl-Delay:
    for the robots.txt file. see http://ur1.ca/l5a0
    for more information. Default is zero, no explicit delay.
disallow: Array of (virtual) directories to disallow. Default is 'main',
    'search', 'message', 'settings', 'admin'. Ignored when site
    is private, in which case the entire site ('/') is disallowed.

api
---

Options for the Twitter-like API.

realm: HTTP Basic Auth realm (see http://tools.ietf.org/html/rfc2617
    for details). Some third-party tools like ping.fm want this to be
    'Identi.ca API', so set it to that if you want to. default = null,
    meaning 'something based on the site name'.

nofollow
--------

We optionally put 'rel="nofollow"' on some links in some pages. The
following configuration settings let you fine-tune how or when things
are nofollowed. See http://en.wikipedia.org/wiki/Nofollow for more
information on what 'nofollow' means.

subscribers: whether to nofollow links to subscribers on the profile
    and personal pages. Default is true.
members: links to members on the group page. Default true.
peopletag: links to people listed in the peopletag page. Default true.
external: external links in notices. One of three values: 'sometimes',
    'always', 'never'. If 'sometimes', then external links are not
    nofollowed on profile, notice, and favorites page. Default is
    'sometimes'.

url
---

These are some options for fine-tuning how and when the server will
shorten URLs.

shortener: URL shortening service to use by default. Users can override
           individually. 'internal' by default.
maxurllength: If an URL is strictly longer than this limit, it will be
           shortened. Note that the URL shortener service may return an
           URL longer than this limit. Defaults to 100. Users can
           override. If set to 0, all URLs will be shortened.
maxnoticelength: If a notice is strictly longer than this limit, all
           URLs in the notice will be shortened. Users can override.
           -1 means the text limit for notices.

router
------

We use a router class for mapping URLs to code. This section controls
how that router works.

cache: whether to cache the router in cache layers. Defaults to true,
    but may be set to false for developers (who might be actively
    adding pages, so won't want the router cached) or others who see
    strange behavior. You're unlikely to need this unless developing..

http
----

Settings for the HTTP client.

ssl_cafile: location of the CA file for SSL. If not set, won't verify
	    SSL peers. Default unset.
curl: Use cURL <http://curl.haxx.se/> for doing HTTP calls. You must
      have the PHP curl extension installed for this to work.
proxy_host: Host to use for proxying HTTP requests. If unset, doesn't
	    do any HTTP proxy stuff. Default unset.
proxy_port: Port to use to connect to HTTP proxy host. Default null.
proxy_user: Username to use for authenticating to the HTTP proxy. Default null.
proxy_password: Password to use for authenticating to the HTTP proxy. Default null.
proxy_auth_scheme: Scheme to use for authenticating to the HTTP proxy. Default null.

plugins
-------

default: associative array mapping plugin name to array of arguments. To disable
	 a default plugin, unset its value in this array.
locale_path: path for finding plugin locale files. In the plugin's directory
	     by default.
server: Server to find static files for a plugin when the page is plain old HTTP.
	Defaults to site/server (same as pages). Use this to move plugin CSS and
	JS files to a CDN.
sslserver: Server to find static files for a plugin when the page is HTTPS. Defaults
	   to site/server (same as pages). Use this to move plugin CSS and JS files
	   to a CDN.
path: Path to the plugin files. defaults to site/path + '/plugins/'. Expects that
      each plugin will have a subdirectory at plugins/NameOfPlugin. Change this
      if you're using a CDN.
sslpath: Path to use on the SSL server. Same as plugins/path.

performance
-----------

high: if you need high performance, or if you're seeing bad
      performance, set this to true. It will turn off some high-intensity code from
      the site.

oldschool
---------

enabled: enable certain old-style user settings options, like stream-only mode,
	 conversation trees, and nicknames in streams. Off by default, and
	 may not be well supported in future versions.
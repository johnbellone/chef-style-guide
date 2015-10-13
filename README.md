# Prelude

This guide is an attempt to document the "best practices" which have
emerged at [Bloomberg][4] during our journey of deploying Chef in a
large enterprise environment. It is intended to serve as a reference
to Bloomberg's infrastructure engineers, as well as the Chef community
as a whole, on how to write high-quality, re-usable infrastructure
code which is capable of being used infront or behind a corporate
firewall.

During the initial phase of bringing Chef into a large enterprise,
especially one with _several thousand_ developers, we realized that
there needed to be some tight constraints put on what (and how) code
was written. Therefore after dragging our feet for a long time we
decided to invest some time in writing this style guide. Since we
contribute and consume the open source cookbooks from the Chef
community it made sense to publish this document. We hope that it can
be of some guidance on how to write confident Chef cookbooks.

# The Chef Style Guide

The core [Chef framework][2] is written in the Ruby programming
language.  The same programming language is used to write
[Chef cookbooks][3]. Therefore, it is natural for us to start with the
best practices of the Ruby programming language. Luckily for us this
has already been done by the amazing Ruby community.

## The Ruby Style Guide

It is very important to understand that this document models itself
from the excellent [Ruby Style Guide][0] which is actively being
maintined by the Ruby community. Most of the principles laid out in
this guide are directly applicable to writing confident infrastructure
code with Chef.

## Table of Contents

* [How to Contribute](#how-to-contribute)
* [Guiding Principles](#guiding-principles)
  * [Infrastructure as Code](#infrastructure-as-code)
  * [Continuous Integration](#continuous-integration)
  * [Unit and Integration Testing](#unit-and-integration-testing)
  * [Service Discovery](#service-discovery)
  * [Semantic Versioning](#semantic-versioning)
* [Platform Considerations](#platform-considerations)
  * [Filesystem Paths](#filesystem-paths)
  * [Service Management](#service-management)
* [Cookbook Design](#cookbook-design)
  * [Attributes](#attributes)
  * [Custom Resources](#custom-resources)
  * [Recipes](#recipes)
  * [Templates](#templates)
* [Cookbook Patterns](#cookbook-patterns)
  * [Application Cookbook](#application-cookbook)
  * [Library Cookbook](#library-cookbook)
  * [Wrapper Cookbook](#wrapper-cookbook)
* [Cookbook Development](#cookbook-development)
  * [Chef Development Kit](#chef-development-kit)
  * [RuboCop](#rubocop)
  * [Test Kitchen](#test-kitchen)

## How to Contribute?
It is very easy, just follow [the contribution guidelines](CONTRIBUTING.md).

## Guiding Principles

### Infrastructure as Code

### Continuous Integration

### Unit and Integration Testing

### Service Discovery

### Semantic Versioning

The open source software development community has begun to gravitate
towards a standard of versioning software to accurately express the
impact of changes between releases. By adhering to a strict policy of
[semantic versioning][15] we are able to indicate to contingent
cookbooks the impact of an impending release.

Of course for cookbooks which are fast-moving this can quickly become
a burden on developer productivity. We rely on our
[continuous integration](#continuous-integration) pipeline to
automatically bump the _patch revision_ of the cookbook for each
promoted release. This means that developers need only to consider the
_major_ and _minor_ revision numbers to express changes in the target
cookbook.

## Platform Considerations

Our aim is to produce quality cookbooks which are capable of
converging in a world of multi-platform operating environments. For
most community cookbooks it is sufficient to support a few flavors of
Linux - usually Ubuntu and CentOS - but in an enterprise environment
we often have to install the same software across _many_ different
platforms.

This often goes beyond simply concatenating a different package CPU
architecture or filename extension. The two examples that we run into
the most often below are _Filesystem Paths_ and _Service Management_.

### Filesystem Paths

While writing an [application cookbook](#application-cookbook) it is
often the case where you may need to write out a configuration file to
disk. In the general case one can simply use Ruby string interpolation
to create the proper configuration file from a few variables. To
illustrate this point let's use the common example of downloading a
file from an HTTP server and then installing that package.

```ruby
url = 'http://mirrors.rit.edu/epel/6/x86_64/collectd-4.10.9-1.el6.x86_64.rpm'
checksum = '549978cc77f9466925701668bfffa147bcb65ef5fa77dad4bee3d85231090010'
basename = File.basename(url)
remote_file "#{Chef::Config[:file_cache_path]/#{basename}" do
  source url
  checksum checksum
end

package 'collectd' do
  source "#{Chef::Config[:file_cache_path]/#{basename}"
  action :upgrade
end
```

The above snippet will work as intended on a POSIX operating system,
but where this will *not* work is on Windows. A typical path on a
Windows file system is addressed with backslashes instead of forward
slashes. A better approach is to use Ruby's built-in API to create
the filesystem path based on the operating system that it is running
on. Let's visit that example again using the [Ruby File.join API][10].

```ruby
url = 'http://mirrors.rit.edu/epel/6/x86_64/collectd-4.10.9-1.el6.x86_64.rpm'
checksum = '549978cc77f9466925701668bfffa147bcb65ef5fa77dad4bee3d85231090010'
basename = File.basename(url)
remote_file File.join(Chef::Config[:file_cache_path], basename) do
  source url
  checksum checksum
end

package 'collectd' do
  source File.join(Chef::Config[:file_cache_path], basename)
  action :upgrade
end
```

This subtle change would now have a correct filesystem separator on
the Windows platform and thus make our recipe a little less error
prone for someone attempting to use it.

### Service Management

The whole purpose of a [application cookbook](#application-cookbook)
is to provide Chef primitives to install and configure an application
on a node. This more often than not requires enabling, starting and
restarting system services. To illustrate our point let's take a look
at the [Cassandra Cluster cookbook][5].

The Cassandra Cluster cookbook is an application cookbook which
installs and configures a node to be a member of a
[Cassandra database cluster][6].  This cookbook has an extremely
simple and straight forward default recipe.  An abbreviated version of
the [Cassandra cookbook's default recipe][7] is given as an example
below:

```ruby
cassandra_config service_name do |r|
  owner node['cassandra-cluster']['service_user']
  group node['cassandra-cluster']['service_group']

  node['cassandra-cluster']['config'].each_pair { |k, v| r.send(k, v) }
  notifies :restart, "cassandra_service[#{name}]", :delayed
end

cassandra_service service_name do |r|
  user node['cassandra-cluster']['service_user']
  group node['cassandra-cluster']['service_group']

  node['cassandra-cluster']['service'].each_pair { |k, v| r.send(k, v) }
end
```

Let's focus on the second resource in this snippet of the recipe. This
is a custom resource which uses the
[Poise Service library cookbook][8] for service management. The
*cassandra_service* resource has attributes which control how the
Cassandra software is installed and where the configuration file is
located. In the default recipe these are driven through the
`node['cassandra-cluster']['service']` attribute Hash.

The Poise Service library cookbook provides a reusable pattern for
creating custom resources that manage services. In practical terms
this means that the same code referenced above will work with the
native system management routines for any of the supported
platforms. Take a look at the table below to further illustrate how
the Poise Service library cookbook would configure the
cassandra_service resource.

| Platform | System Management |
| -------- | ----------------- |
| Ubuntu 12.04 | Upstart |
| Ubuntu 14.04 | Upstart |
| Ubuntu 16.04 | Systemd |
| CentOS 5.11  | SysV |
| CentOS 6.7   | Upstart |
| CentOS 7.1   | Systemd |
| Solaris | SysV |
| AIX | SysV |

Furthermore, the Poise Service library allows for the "service
provider" to be specified as a custom attribute or the default set
through a node attribute. So, for instance, if we would like to use
the [Runit service management framework][9] instead of the native
provider that can simply be done by including a new cookbook and
setting a node attribute in a wrapper cookbook.

```ruby
node.default['poise-service']['provider'] = 'runit'
```

Why is this important? By using the Poise Service library cookbook we
can abstract away the concerns of service provider management and
build a clear and concise cookbook. It reads a lot easier, and it is a
whole lot more flexible out of the box. It also means that the same
cookbook can be used on all of the above platforms without the need
for the management of service provider templates. This is an
_extremely valuable_ pattern when you are managing dozens of
application cookbooks. We now have a *single* library cookbook instead
of a dozen application cookbooks with different service management
templates.

## Cookbook Design

Since we have a diverse set of platform requirements for most of our
cookbooks we have made several decisions regarding the usage of node
attributes, custom resources and templates. Additionally, because our
engineers do *not* have knife access this limits the custom values
which could be set in node attributes. It also means that we must
support looking up variable data such as data bags through an HTTP
service.

### Ohai

In the general case we prefer writing custom plugins which populate
Ohai attributes from either the operating system or external services.
This is important as it allows us to describe the state of a system
with the `ohai` command-line tool outside of a Chef convergence.

### Attributes

By limiting the default value of attributes it allows for deployment
specific overrides to happen from outside of a cookbook. There should
be very few cases where this is used instead of a
[service discovery](#service-discovery) mechanism, but in a pinch it
may be necessary.

### Custom Resources

### Recipes

### Templates

## Cookbook Patterns

### Library Cookbook

A library cookbook abstracts common patterns into resources and
providers which can be used to build both wrapper cookbooks and
application cookbooks.

A great example of a library cookbook is the [libarchive cookbook][11]
which provides a Chef resource primitive to manage compressed archives
of all different formats. This makes it extremely useful to write
application cookbooks that are agnostic to the underlying compression
format. The example below downloads a compressed archive of the
[GitHub Hub command][12] for Linux x86_64 and extracts it to the /opt
directory.

```ruby
archive_url = 'https://github.com/github/hub/releases/download/v2.2.1/hub-linux-amd64-2.2.1.tar.gz'
remote_file File.join(Chef::Config[:file_cache_path], File.basename(archive_url)) do
  source archive_url
end

libarchive_file File.join(Chef::Config[:file_cache_path], File.basename(archive_url)) do
  extract_to '/opt/hub/2.2.1'
end
```

A more complex example of using the libarchive_file resource as a
primitive can be found in the [libartifact cookbook][12]. This
cookbook goes a step further and manages application artifacts on disk
using symbolic links. It utilizies the libarchive_file resource for
managing the extraction of compressed artifacts.

### Application Cookbook

The application cookbook is the most common cookbook pattern. Its
purpose is to install, configure and manage the lifecycle of an
application on a node. Most cookbooks which are publically available
on the [Chef Supermarket][13] are of this type.

The complexity of application cookbooks can vary very widely. A
cookbook such as our own [Collectd cookbook][14] installs and
configures the collectd monitoring daemon. Because there are several
tuning knobs on the daemon itself we take the approach of breaking out
additional Chef resource primitives to manage the configuration and
service separately. There is an additional resource which manages the
configuration of collectd plugins.

This process of modeling an application cookbook with several
primitives allows for the maximum flexibility for testing and
deployment. Because we are testing the *validity* of the input
properties of a resource we're able to fail the Chef convergence prior
to configuration being modified on the target.

### Wrapper Cookbook

In an enterprise environment if the decision is made to include a
public cookbook available on the [Chef Supermarket][13] it almost
always should be included via a wrapper cookbook. If the underlying
application cookbooks are flexible enough - that is, the Chef resource
primitives appropriately model the configuration and application -
writing a wrapper cookbook is very straightforward and simple.

There are a few types of wrapper cookbooks which are generally accepted
as best practices within the Chef community. These patterns represent
a purposeful adaptation of the concept of a wrapper cookbook.

A base cookbook is a type of wrapper cookbook which is in the expanded
run-list of each and every node within an organization. The base
cookbook itself generally wraps core cookbooks which harden and
configure the operating system itself. It is also the place where
mirrors and the chef-client are configured.

A cluster cookbook is a type of wrapper cookbook which targets a
specific configuration of a cluster of nodes. The cluster cookbook may
set purposeful node attributes to fine tune the underlying application
running on the cluster. A recipe within a cluster cookbook is generally
one of the only recipes directly applied to a node's run-list.

## Cookbook Development

### Chef Development Kit

### RuboCop

### Test Kitchen

[0]: https://github.com/bbatsov/ruby-style-guide
[1]: https://github.com/bbatsov/rubocop
[2]: https://github.com/chef/chef
[3]: http://docs.chef.io/cookbooks.html
[4]: http://www.bloomberglabs.com
[5]: https://github.com/johnbellone/cassandra-cluster-cookbook
[6]: http://cassandra.apache.org
[7]: https://github.com/johnbellone/cassandra-cluster-cookbook/blob/master/recipes/default.rb
[8]: https://github.com/poise/poise-service
[9]: https://github.com/poise/poise-service-runit
[10]: http://ruby-doc.org/core-2.2.0/File.html#method-c-join
[11]: https://github.com/reset/libarchive-cookbook
[12]: https://github.com/github/hub
[13]: https://supermarket.chef.io/
[14]: https://github.com/johnbellone/collectd-cookbook
[15]: http://semver.org/

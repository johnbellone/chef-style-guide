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
  * [Code Reusability](#code-reusability)
  * [Unit and Integration Testing](#unit-and-integration-testing)
  * [Service Discovery](#service-discovery)
* [Platform Considerations](#platform-considerations)
  * [Filesystems](#filesystems)
  * [Service Management](#service-management)
* [Cookbook Patterns](#cookbook-patterns)
  * [Application Cookbook](#application-cookbook)
  * [Library Cookbook](#library-cookbook)
  * [Wrapper Cookbook](#wrapper-cookbook)
* [Cookbook Design](#cookbook-design)
  * [Attributes](#attributes)
  * [Custom Resources](#custom-resources)
  * [Recipes](#recipes)
  * [Templates](#templates)
* [Cookbook Tooling](#cookbook tooling)
  * [FoodCritic](#foodcritic)
  * [RSpec](#rspec)
  * [RuboCop](#rubocop)
  * [Travis CI](#travis-ci)
  * [Test Kitchen](#test-kitchen)
  * [YARD](#yard)

## How to Contribute?
It is very easy, just follow [the contribution guidelines](CONTRIBUTING.md).

## Guiding Principles

### Infrastructure as Code

### Code Reusability

### Unit and Integration Testing

### Service Discovery

## Platform Considerations

### Filesystems

### Service Management

## Cookbook Patterns

### Application Cookbook

### Library Cookbook

### Wrapper Cookbook

## Guiding Principles

### Attributes

### Custom Resources

### Recipes

### Templates

## Cookbook Tooling

### FoodCritic

### RSpec

### RuboCop

### Travis CI

### Test Kitchen

### YARD

[0]: https://github.com/bbatsov/ruby-style-guide
[1]: https://github.com/bbatsov/rubocop
[2]: https://github.com/chef/chef
[3]: http://docs.chef.io/cookbooks.html
[4]: http://www.bloomberglabs.com

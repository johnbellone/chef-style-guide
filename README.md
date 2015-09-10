# Bloomberg Chef Style Guide

This style guideline draws a lot of inspiration from the
[Ruby Style Guide][0]; the author of the guide also maintains the
widely used [RuboCop][1] static code analysis framework. In order to
write high-quality, re-usable Chef cookbooks it is _very important_ to
understand the best practices around the Ruby language. For that very
reason we use many of the same tools (and language rules) when testing
our Chef cookbooks.

The core [Chef framework][2] is written in the Ruby programming
langauge. The same programming language is used for writing
[Chef cookbooks][3], and thus, we are able to apply the idiomatic
princples of the [Ruby Style Guide][0]. The Ruby Style Guide was a
direct inspiration for the layout and format of this document, and in
the context of Chef, we intent for this guide to be a natural
extension.

## Table of Contents

* [How to Contribute](#how-to-contribute)
* [Guiding Principles](#guiding-principles)
  ** [Infrastructure as Code](#infrastructure-as-code)
  ** [Code Reusability](#code-reusability)
  ** [Unit and Integration Testing](#unit-and-integration-testing)
* [Repository Layout](#repository-layout)
  ** [FoodCritic](#foodcritic)
  ** [RSpec](#rspec)
  ** [RuboCop](#rubocop)
  ** [Travis CI](#travis-ci)
  ** [Test Kitchen](#test-kitchen)
  ** [YARD](#yard)
* [Cookbook Patterns](#cookbook-patterns)
  ** [Application Cookbook](#application-cookbook)
  ** [Library Cookbook](#library-cookbook)
  ** [Wrapper Cookbook](#wrapper-cookbook)
* [Cookbook Design](#cookbook-design)
  ** [Attributes](#attributes)
  ** [Custom Resources](#custom-resources)
  ** [Recipes](#recipes)
  ** [Templates](#templates)

## How to Contribute?
It is very easy, just follow [the contribution guidelines](CONTRIBUTING.md).

## Guiding Principles

### Infrastructure as Code

### Code Reusability

### Unit and Integration Testing

## Repository Layout

### FoodCritic

### RSpec

### RuboCop

### Travis CI

### Test Kitchen

### YARD

## Cookbook Patterns

### Application Cookbook

### Library Cookbook

### Wrapper Cookbook

## Guiding Principles

### Attributes

### Custom Resources

### Recipes

### Templates

[0]: https://github.com/bbatsov/ruby-style-guide
[1]: https://github.com/bbatsov/rubocop
[2]: https://github.com/chef/chef
[3]: http://docs.chef.io/cookbooks.html

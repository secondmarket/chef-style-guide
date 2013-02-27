SecondMarket Chef Style Guide
=============================

This project contains the Chef Style Guide we use at SecondMarket. Some of these rules are enforced by FoodCritic; some others are not.

System and Component Naming
===========================

Name things uniformly for their system and component. For the ganglia master,

* attributes: `node['ganglia']['master']`
* recipe: `ganglia::master`
* role: `ganglia-master`
* directories: `ganglia/master` (if specific to component), `ganglia` (if not). For example: `/var/log/ganglia/master`

(The foregoing was shamelessly cribbed from Ironfan)

Name attributes after the recipe in which they are primarily used. e.g. `node['postgresql']['server']`

Default Recipe
==============

Don't use the default recipe (leave it blank). Instead, create recipes called `server` or `client` (or other).

Resource Parameter Order
========================

Follow this order for information in each resource declaration:

*    Source
*    Cookbook
*    Resource ownership
*    Permissions
*    Notifications
*    Action

Example:

    template "/tmp/foobar.txt" do
      source "foobar.txt.erb"
      owner  "someuser"
      group  "somegroup"
      mode   00644
      variables(
        :foo => 'bar'
      )
      notifies :reload, 'service[whatever]'
      action :create
    end

File Modes
==========

Always specify all five digits of the file mode, and not as a string.

Wrong:

    mode "644"

Right:

    mode 00644

Always Specify Action
=====================

In each resource declarations always specify the action to be taken:

Wrong:

    package "monit"

Right:

    package "monit" do
      action :install
    end

FoodCritic Linting
==================

It goes without saying that all cookbooks should pass FoodCritic rules before being uploaded.

    $ foodcritic -f all your-cookbook

should return nothing.

Symbols or Strings?
===================

Even though the controversial FC001 Foodcritic rule has now been removed, we still prefer strings over symbols. Please retrofit old cookbooks as you come across them.

Wrong:

default[:foo][:bar] = 'baz'

Right:

default['foo']['bar'] = 'baz'

Constructs to Avoid
===================

* `node.set_unless` - Can lead to weird behavior if the node object had something set. Avoid unless altogether necessary (one example where it's necessary is in `node['postgresql']['server']['password']`)
* `if node.run_list.include?("foo")` i.e. branching in recipes based on what's in the node's run list. Better and more readable to use a feature flag and set its precedence appropriately.
* `node['foo']['bar']` i.e. setting normal attributes without specifying precedence. This is deprecated in Chef 11, so either use `node.set['foo']['bar']` to replace its precedence in-place or choose the precedence to suit.

Useful Links
============

* [Ironfan Style Guide](https://github.com/infochimps-labs/ironfan/wiki/style_guide)
* [FoodCritic](http://acrmp.github.com/foodcritic/)

License and Authors
===================

* Author:: Julian Dunn (<jdunn@secondmarket.com>)

* Copyright:: 2013, SecondMarket Labs, LLC.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

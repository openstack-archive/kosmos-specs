..

This work is licensed under a Creative Commons Attribution 3.0 Unported License.
http://creativecommons.org/licenses/by/3.0/legalcode

..
  This template should be in ReSTructured text. The filename in the git
  repository should match the launchpad URL, for example a URL of
  https://blueprints.launchpad.net/kosmos/+spec/awesome-thing should be named
  awesome-thing.rst .  Please do not delete any of the sections in this
  template.  If you have nothing to say for a whole section, just write: None
  For help with syntax, see http://sphinx-doc.org/rest.html
  To test out your formatting, see http://www.tele3.cz/jbar/rest/rest.html

=============================
Database Changes
=============================

https://blueprints.launchpad.net/kosmos/+spec/database-changes

Many GSLB backends have specific parameters for load-balancer policies.
Instead of just being a CNAME record in a DNS server as expected with
the Neutron LBaaS + Designate solution.

Also at time there is not just one Pool associated with a load-balancer
policy, there could be more than one. Like Active and Passive for disaster
recovery. So we need to move the pool-id out of loadbalancer table and
make it a separate relational table.

Problem description
===================

There is a requirement for introduction of loadbalancer_parameters in to
the kosmos database as some GSLB backends have specific attributes associated
with a GSLB Policy.  As current kosmos database schema doesn't accomodate
parameters for load_balancer, we need to add a new table named,
load_balancer_parameters for this.

We also need to take pool_id from load_balancer table as there could be
more than one pool associated with a policy. For example Active/Standy
policies would always have a standby pool that should be defined for the
policy.

domain_id in all the artifact table will be modified to accept null so
that we implement only if required.

Proposed change
===============

We need to make changes to the following:

db/tables.py
------------

Add a new table loadbalancer_parameters with the following columns:
    1. loadbalancer_id [FK from loadbalancers table]
    2. parameter_key [Type: String(255)]
    3. prameter_value [Type: String(255)]

Remove pool_id from loadbalancers table

Add a new table loadbalancer_pools with the following columns:
    1. loadbalancer_id [FK from loadbalancers table]
    2. pool_id [FK from pools table]
    3. loadbalancer_pool_type [Type: LB_POOL_TYPES; Default: ACTIVE]

Add LB_POOL_TYPES as an enum with the following possible values:
    1. ACTIVE
    2. PASSIVE

Remove domain_id from the following tables:
    1. pools
    2. pool_members
    3. loadbalancers
    4. monitors

Alter the flavour_id in the loadbalancer table from String(32) to UUID()

Alembic Versions
----------------

Generate new Version file using Alembic.  Make sure it has both upgrade()
and downgrade() methods implemented.

objects/loadbalancer.py
-----------------------

Remove pool_id to be nullable=False in loadbalancer.py.

Add pools and parameters in fields section. These fields should default
and an empty array.

Change the version to 1.1.

objects/base.py
------------------

Change domain_id to be nullable=True in KosmosOwnedObject.

objects
-------

Add below py files:

    1. loadbalancer_pool.py
    2. loadbalancer_parameter.py

objects/__init__.py
-------------------

Add loadbalancer_pool and loadbalancer_parameters to import statements

tests/unit/objects
------------------

Update test_kosmos_object_hashes.py with the new hashes.

Add test cases for all the objects.

Update the base test case.

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  krevuri

Secondary assignees:
  ma501v
  pr8721
  praveen-komakula
  pradeepkumar.ks

Milestones
----------

Target Milestone for completion:
  Pike

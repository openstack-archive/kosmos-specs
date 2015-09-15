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

=====================
 System Architecture
=====================

Overview
========

The system should be made up of a few services, that are small, and horizontally scalable.

The system should be the following:
* Scalable
* Fault Tolerant
* Use other OpenStack projects as first class citizens
    - For the GSLB appliance drivers the default should be `Designate`_
    - For regional pool members Neutron `LBaaS`_ should be the default

.. note:: This is for the MVP. Post MVP we need to also be able to run as a global service.


Overview Diagram
----------------

.. graphviz:: sysarch/sysarch-diagram-overview.dot


Services
--------

+-----------------------+--------------------------+---------------------------+-------+
| service name          | deployment model         | purpose                   | notes |
+=======================+==========================+===========================+=======+
| kosmos-api            | multiple                 | Configuration of GSLBs    |       |
+-----------------------+--------------------------+---------------------------+-------+
| kosmos-conductor      | multiple - single region | Database Access           |       |
+-----------------------+--------------------------+---------------------------+-------+
| kosmos-status-checker | multiple - global        | Check status of endpoints |       |
+-----------------------+--------------------------+---------------------------+-------+
| kosmos-engine         | multiple - single region | Business Logic            |       |
+-----------------------+--------------------------+---------------------------+-------+

kosmos-api
^^^^^^^^^^

The API would be a WSGI service that implements the API Spec previously approved.

It should follow the standard OpenStack patterns (use Keystone / oslo.middleware / oslo.context)

kosmos-conductor
^^^^^^^^^^^^^^^^

This will act as a single point of access to the DB, allowing for consistent data validation.

As results are fed in from status-checkers, then engine will decide if an endpoint is suitable to be included in the pool. As it adds and removes nodes, it will
update both the database, and the GSLB appliance, via the plugin. (e.g. if using the Designate plugin it would remove the endpoint's IP from the record)

.. warning:: The logging component described below may be cut in the MVP

There is a logging component that could be a separate DB, a timeseries columnar DB, elastic search or other storage system
This will store a section of history for the pools, to allow users to see when / why endpoints were added / removed from the load balancer.

.. graphviz:: sysarch/sysarch-diagram-conductor.dot

kosmos-status-checker
^^^^^^^^^^^^^^^^^^^^^

This is a worker style service that will run status checks on the defined endpoints.

This will have a plugin interface to allow for more checks to the loaded by different plugins, or for custom checks to be written by the deployer (using standard OpenStack plugin patterns)

For example, any pool-members / endpoints that are LBaaS instances will not call into the VIP, but will call the LBaaS V2 API to get the health of the regional members.

.. graphviz:: sysarch/sysarch-diagram-status-checker.dot

kosmos-engine
^^^^^^^^^^^^^

This is where all the business logic resides. This service will consume status results and decide if an endpoint should be removed / added.
This service will then use the plugin loaded for the GSLB backend to orchestrate this.

The plugins will allow different types of GSLB appliances / services to be used, and will use standard OpenStack plugin patterns.

.. graphviz:: sysarch/sysarch-diagram-engine.dot


Entity Relationship Diagram
---------------------------

.. graphviz:: sysarch/erd-diagram.dot


Example
-------

This is an example flow of information, when we are using Designate and checking the health info of a Neutron LBaaS load balancer.
The plugin components are excluded for clarity, but would be between the "Status Check" and "Engine" components.

.. seqdiag:: sysarch/example-flow.diag

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  kosmos-drivers

Milestones
----------

Target Milestone for completion:
  M-2

.. _Designate: http://wiki.openstack.org/wiki/Designate
.. _LBaaS: http://https://wiki.openstack.org/wiki/Neutron/LBaaS
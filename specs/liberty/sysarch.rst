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

kosmos-conductor
^^^^^^^^^^^^^^^^

This will act as a single point of access to the DB, allowing for consistent data validation.

kosmos-status-checker
^^^^^^^^^^^^^^^^^^^^^

This is a worker style service that will run recurring tasks - i.e. status checks on the defined endpoints

This will have a plugin interface to allow for more checks to the loaded by different plugins, or for custom checks to be written by the deployer

kosmos-engine
^^^^^^^^^^^^^

This is where all the business logic resides. This service will consume status results and decide if an endpoint should be removed / added.
This service will then use the plugin loaded for the GSLB backend to orchestrate this.

Diagram
-------

.. graphviz:: sysarch-diagram.dot

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  kosmos-drivers

Milestones
----------

Target Milestone for completion:
  M-1

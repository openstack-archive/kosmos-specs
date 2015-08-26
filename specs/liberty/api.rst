..

This work is licensed under a Creative Commons Attribution 3.0 Unported License.
http://creativecommons.org/licenses/by/3.0/legalcode

..
  This template should be in ReSTructured text. The filename in the git
  repository should match the launchpad URL, for example a URL of
  https://blueprints.launchpad.net/gslb/+spec/awesome-thing should be named
  awesome-thing.rst .  Please do not delete any of the sections in this
  template.  If you have nothing to say for a whole section, just write: None
  For help with syntax, see http://sphinx-doc.org/rest.html
  To test out your formatting, see http://www.tele3.cz/jbar/rest/rest.html

=====
 API 
=====

https://blueprints.launchpad.net/gslb/+spec/api

We need an API, so defining it is a good start.

Example JSON Snippets
=====================

Load Balancer JSON Snippet
--------------------------

 .. code-block:: json

    {
      "id": "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "name": "Main Website",
      "fqdn": "www.gslb.example.com.",
      "domain_name": "example.com.",
      "flavor": "example_1",
      "status": "ACTIVE",
      "links": {
        "self": "http://example.gslb.openstack.org/v0.1/gslbs/f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
        "pools": "http://example.gslb.openstack.org/v0.1/gslbs/f9fc6210-8efa-4492-9b4f-d87ba98f35b8/pools",
        "status": "http://example.gslb.openstack.org/v0.1/gslbs/f9fc6210-8efa-4492-9b4f-d87ba98f35b8/status"
      }
    }


Pool JSON Snippet
-----------------

 .. code-block:: json

    {
      "id": "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "name": "Main Website Pool",
      "flavor": "example_1",
      "status": "ACTIVE",
      "members":["SEE MEMBER SNIPPETS BELOW"],
      "links": {
        "self": "http://example.gslb.openstack.org/v0.1/pools/f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
        "pool_members": "http://example.gslb.openstack.org/v0.1/pools/f9fc6210-8efa-4492-9b4f-d87ba98f35b8/pool_members",
        "status": "http://example.gslb.openstack.org/v0.1/pools/f9fc6210-8efa-4492-9b4f-d87ba98f35b8/status"
      }
    }

Pool Member JSON Snippet - Neutron LBaaS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id": "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "name": "Main Website",
      "type": "neutron_lbaas_v2",
      "region": "RegionTwo",
      "neutron_id": "df40954c-982a-4778-a1b2-32b93cf75af9",
      "status": "ACTIVE",
      "endpoints": [
        "10.10.0.1"
      ]
    }


Pool Member JSON Snippet - Neutron Port
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id": "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "name": "Main Website",
      "type": "neutron_port",
      "region": "RegionTwo",
      "neutron_id": "78fa9c7f-f200-49df-912b-ae4679fd21e9",
      "status": "ACTIVE",
      "endpoints": [
        "10.10.0.1"
      ]
    }

Pool Member JSON Snippet - IP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id": "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "name": "Main Website",
      "type": "IP",
      "status": "ACTIVE",
      "endpoints": [
        "10.10.0.1"
      ]
    }


Monitor JSON Snippet - TCP Basic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id" : "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "type" : "TCP",
      "delay" : 20,
      "timeout": 10,
      "max_retries": 3,
      "port" : 22,
      "name": "ssh_generic"
    }
 
Monitor JSON Snippet - Ping Basic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id" : "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "type" : "PING",
      "delay" : 20,
      "timeout": 10,
      "max_retries": 3,
      "name": "ping_generic"
    }

Monitor JSON Snippet - HTTP
^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id" : "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "delay": 20,
      "timeout": 10,
      "max_retries": 3,
      "type": "HTTP",
      "http_method": "GET",
      "url_path": "/healthchecks",
      "receive_string": "SUCCESS",
      "expected_codes": [
        200,
        202
      ],
      "name": "http-generic",
      "port" : 80
    }

Monitor JSON Snippet - HTTPS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 .. code-block:: json

    {
      "id" : "f9fc6210-8efa-4492-9b4f-d87ba98f35b8",
      "delay": 20,
      "timeout": 10,
      "max_retries": 3,
      "type": "HTTPS",
      "insecure": false,
      "http_method": "POST",
      "payload": {
        "content_type" : "application/json",
        "content" "{'demo': 'JSON', 'Blob': 'of data'}"
        }
      },
      "ca_cert": "<CA_CERT_BLOB>"
      "url_path": "/healthchecks",
      "receive_string" "SUCCESS",
      "expected_codes": [
        200,
        202
      ],
      "name": "https-generic",
      "port" : 443
    }

Endpoints
=========

All of these endpoints will have the usual CRUD methods available

/v0.1/gslbs
-----------

Returns a list of GLSBs

/v0.1/gslbs/<uuid>
------------------

Returns a GLSB

/v0.1/gslbs/<uuid>/pools
------------------------

Returns GLSB's list of pools

/v0.1/gslbs/<uuid>/history
--------------------------

Returns GLSB's history (up/down/degraded etc)

.. note:: This may or may not make MVP. This should only be a limited history, and for MVP my just be a "last updated" field

/v0.1/pools
-----------

Returns a list of Pools

/v0.1/pools/<uuid>
------------------

Returns a pool

/v0.1/pools/<uuid>/monitors
---------------------------

Returns a pool's list of monitors

/v0.1/pools/<uuid>/pool_members
-------------------------------

Returns a pool's list of members

/v0.1/pools/<uuid>/pool_members/<uuid>
--------------------------------------

Returns a pool member

/v0.1/pools/<uuid>/pool_members/<uuid>/status
---------------------------------------------

Returns a pool members status (Up/Down etc)

/v0.1/pools/<uuid>/pool_members/<uuid>/monitors
-----------------------------------------------

Returns a members list of monitors

/v0.1/pools/<uuid>/status
-------------------------

Returns the status of a pool

/v0.1/monitors
--------------

Returns a list of health monitors

/v0.1/monitors/<uuid>
---------------------

Returns a monitor

Implementation
==============

Assignee(s)
-----------


Primary assignee:
  gslb-core

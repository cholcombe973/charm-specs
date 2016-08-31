Add the cephx group permissions to the ceph broker pool creation function.
..
  Copyright 2016, Canonical UK

  This work is licensed under a Creative Commons Attribution 3.0
  Unported License.
  http://creativecommons.org/licenses/by/3.0/legalcode

..
  This template should be in ReSTructured text. Please do not delete
  any of the sections in this template.  If you have nothing to say
  for a whole section, just write: "None". For help with syntax, see
  http://sphinx-doc.org/rest.html To test out your formatting, see
  http://www.tele3.cz/jbar/rest/rest.html

===============================
Ceph Mon Broker Permissions
===============================

Allow restricted CephX keys to be generated for clients connecting to Ceph.

Problem Description
===================

Currently today whenever a client requests access to the Ceph cluster they
are given a key that has access to every pool in the cluster.  This is not
ideal.

Proposed Change
===============

An extension will be made to the ceph-mon charm broker.  This extension will
allow clients to specify a group they would like to belong to. This will be
optional.  If not set it will default to none and go down the normal path
with the cephx key creation. If it is set then a key will be created and the
groups=[pool] stored in the ceph monitor cluster.  Whenever a new key or pool
is created that asks for permission to a group, ceph-mon broker will walk
through all the keys in the monitor cluster and adjust the permissions for the
ones that asked to have access to a group or groups.


Alternatives
------------

None currently.

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  Chris Holcombe

Gerrit Topic
------------

    git-review -t pool-permissions

Work Items
----------

 * Modify the broker json protocol to allow for an optional group list.
 * Allow existing clients to create cephx keys without any change
 * Create a group mapping in the ceph monitor cluster.
 * Modify the existing cephx keys when a new pool is created with a group
 list

Repositories
------------
This will use the existing ceph-mon repository:
https://github.com/openstack/charm-mon

Documentation
-------------

Documentation will be added to the README.md as part of the normal workflow.

Security
--------

This should tighten security on the Ceph cluster.

Testing
-------

* Deploy ceph-mon and ceph-osd using juju.
* Deploy ceph-rgw and relate it to ceph-mon
* Verify that ceph-rgw's cephx only has access to the pools it requested.

Dependencies
============
None

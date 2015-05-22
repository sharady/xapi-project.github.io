---
title: FCoE capable NICs
layout: default
design_doc: true
revision: 1
status: proposed
---

It has been possible to identify the NICs of a Host which can support FCoE.
This property can be listed in PIF object under capabilities field with key `FCoE`.

Introduction
------------

* FCoE supported on a NIC is a hardware property. With the help of dcbtool, we can identify which NIC support FCoE.
* The new field capabilites will be `(string -> string) map` in PIF object. For FCoE capability in PIF capabilties will have field set to `"FCoE" -> "Unsupported"`.
* `Supported` string will state NIC supports FCoE. `Unsupported` string will state NIC does not supports FCoE.
* Capabilties `(key, value)` field will be ReadOnly, This field cannot be modified by user.

PIF Object
-------

New field:
* Field `PIF.capabilties` will be type `(string -> string) map`.
* Default value of PIF capabilties will have field set to `"FCoE" -> "Unsupported"`.

Xapi Changes
------

* Set the field capabilities `FCoE` key to `Unsupported` or `Supported` depending on output of xcp-networkd call `is_fcoe_supported`.
* Field capabilities `FCoE` can be set during `introduce_internal` when the PIF is created.
* Field capabilties `FCoE` can be updated during `refresh_all` during PIF scan.
* The above field will be set everytime when xapi-restart.

XCP-Networkd Changes
------

New function:
* Boolean `bool is_fcoe_supported (string)`
  Argument: the device_name for the PIF.
* This function returns `true` or `false` depending on dcbtool output for the device provided.

Defaults, Installation and Upgrade
------------------------
* Any newly introduced PIF will have its capabilties field set to `"FCoE" -> "Unsupported"` until xcp-networkd call `is_fcoe_supported` states FCoE is supported on the NIC.
* It includes PIFs obtained after a fresh install of Xenserver, as well as PIFs created using `PIF.introduce` then `PIF.scan`.
* During an upgrade Xapi Restart will call `refresh_all` which then populate the capabilties field set to `"FCoE"` -> `"Unsupported"`


Command Line Interface
----------------------

* The `PIF_metrics.fcoe_supported` field is exposed through `xe pif-list` and `xe pif-param-list` as usual.

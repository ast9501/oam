.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. SPDX-License-Identifier: CC-BY-4.0
.. Copyright (C) 2020 highstreet technologies and others

.. contents::
   :depth: 3
..

SDN-R WT Service Configuration parameters
=========================================

ODL is operated as a cluster. The configuration settings must be the
same for each cluster node.

-  `Sections <#SDN-RWTServiceConfigurationparameters-S>`__

   -  `toggleAlarmFilter <#SDN-RWTServiceConfigurationparameters-t>`__
   -  `devicemonitor <#SDN-RWTServiceConfigurationparameters-d>`__

Backgrounds regarding the container inter structure is described
in \ `SDN-R Docker Image configuration <sdnr_Docker_Image_configuration.rst>`__.

The configuration information of sdnr wireless transport parameters are
in a single file.

For different devicemanager services, like DeviceMonitor there  are
individual sections in the configuration file available.

Configuration file location: 
***$ODL\_HOME/etc/devicemanager.properties***

If SDN-R WT is starting

-  and the file exists, the configuration is used. 
-  if it does not exist it will be created with the default parameters.

Below an example of the content.

- Example
- 
  .. code-block:: javascript
    :linenos:

    [toggleAlarmFilter]
    taEnabled=true
    taDelay=3000
   
    [es]
    esCluster=sdnr
    esArchiveCheckIntervalSeconds=0
    esArchiveLifetimeSeconds=2592000

    [dcae]
    dcaeUrl=off
    dcaeUserCredentials=admin:admin
    dcaeHeartbeatPeriodSeconds=120

    [aai]
    aaiUrl=off
    aaiUserCredentials=
    aaiHeaders=["X-TransactionId: 9999"]
    aaiDeleteOnMountpointRemove=false
    aaiTrustAllCerts=false
    aaiPropertiesFile=
    aaiApiVersion=aai/v13
    aaiApplicationId=SDNR
    aaiClientConnectionTimeout=30000
    aaiPcks12ClientCertFile=
    aaiPcks12ClientCertPassphrase=

    [pm]
    pmEnabled=true
    pmCluster=sdnr

    [devicemonitor]
    SeverityconnectionLossOAM=Major
    SeverityconnectionLossMediator=Major
    SeverityconnectionLossNeOAM=Major

Sections
--------

toggleAlarmFilter
~~~~~~~~~~~~~~~~~

Configure toggle alarm filter.

taEnabled=true taDelay=3000

+-----------------+---------------+---------------+----------------+------------------------------------------------------+
| **Parameter**   | **Values**    | **Default**   | **Unit**       | **Description**                                      |
+=================+===============+===============+================+======================================================+
| taEnabled       | true, false   | false         |                | Enable or disable this service                       |
+-----------------+---------------+---------------+----------------+------------------------------------------------------+
| taDelay         | number        |               | milliseconds   | Integration time to take over the new alarm status   |
+-----------------+---------------+---------------+----------------+------------------------------------------------------+

devicemonitor
~~~~~~~~~~~~~

Configure alarm severity of related alarms, generated by Device Monitor.

`SDNC-616 <https://jira.onap.org/browse/SDNC-616>`__ - SDN-R WT app need to change alarm severity "Configurable" for ConnectionLossxxx alarm family (received from Mediator) , when it passed to DCAE VES collector.


Syntax: Parameter=Value

Example: SeverityconnectionLossOAM=Major

+----------------------------------+-----------------------------------------------+---------------+------------+--------------------------------------------------------------+
| Alarm                            | **Values**                                    | **Default**   | **Unit**   | **Description**                                              |
+==================================+===============================================+===============+============+==============================================================+
| SeverityconnectionLossOAM        | NonAlarmed, Warning, Minor, Major, Critical   | Major         |            | SDN-Controller <> Mediator                                   |
|                                  |                                               |               |            | Mountpoint monitoring. Indicates a not connected mounpoint   |
+----------------------------------+-----------------------------------------------+---------------+------------+--------------------------------------------------------------+
| SeverityconnectionLossMediator   | NonAlarmed, Warning, Minor, Major, Critical   | Major         |            | SDN-Controller <> NetworkElement                             |
|                                  |                                               |               |            | Device monitoring. No LTPs provided                          |
+----------------------------------+-----------------------------------------------+---------------+------------+--------------------------------------------------------------+
| SeverityconnectionLossNeOAM      | NonAlarmed, Warning, Minor, Major, Critical   | Major         |            | SDN-Controller <> NetworkElement                             |
|                                  |                                               |               |            | Device monitoring. SSH Connetion, Core model not answering   |
+----------------------------------+-----------------------------------------------+---------------+------------+--------------------------------------------------------------+

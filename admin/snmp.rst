.. _snmp:

####
SNMP
####

SNMP Drivers
============

Various SNMP devices might require special measures to get information, e.g. 
some devices provide additional information for interfaces only under vendor OIDs, etc. 
To address this, |product_name| provides a concept of SNMP drivers. SNMP driver is detected automatically. 

If SNMP driver was not automatically detected, it's possible to set it manually by specifying driver name in custom attribute ``snmp.driver`` on a node. 

Possible SNMP driver names are: 
  - AT
  - BAYSTACK
  - CAMBIUM-CNPILOT
  - CAMBIUM-EPMP
  - CATALYST-2900XL
  - CATALYST-GENERIC
  - CISCO-ESW
  - CISCO-GENERIC
  - CISCO-NEXUS
  - CISCO-SB
  - CISCO-WLC
  - DELL-PWC
  - DLINK
  - ERS8000
  - EXTREME
  - H3C
  - HPSW
  - IGNITENET
  - JUNIPER
  - MIKROTIK
  - MOXA-EDR
  - NET-SNMP
  - NETONIX
  - NETSCREEN
  - NTWS
  - OPTIX
  - PING3
  - PROCURVE
  - QTECH-OLT
  - RITTAL
  - SAF-INTEGRA-B
  - SYMBOL-WS
  - TB
  - UBNT-EDGESW
  - UBNT-AIRMAX
  - WESTERSTRAND


.. _mib_explorer:

MIB Explorer
============

MIB browser shows all loaded MIB configurations, and allows to run :term:`SNMP`
walk on a selected node :term:`nodes <Node>`. Node can be selected in browser
by selecting :guilabel:`Set node object...` option in view menu or by opening
:guilabel:`MIB Explorer` from node menu.

.. figure:: _images/MIB_Explorer.png

To run walk user should select line of tree from were will be requested all data.
By walk will be requested all OID subtree of selected item.

After walk is done it's results will shown in the table below.

.. figure:: _images/snmp_walk_result_menu.png

There are next options available for results:
  - Copy result line to clipboard
  - Copy name of selected line to clipboard
  - Copy type of selected line to clipboard
  - Copy value of selected line to clipboard
  - Export selected lines to CSV
  - Show selection in MIB tree
  - Create DCI from selected item

SNMP Trap Configuration
=======================

In this view is configured which event will be generated on exact trap OID and
which OID data will be used as event parameter data.


.. figure:: _images/snmp_trap_configuration.png


In SNMP Trap mapping configuration window can be set next parameters:

  - Description of mapping rule
  - Trap OID or trap OID group with many subtree OIDs, matching OID will be given
    to event as $1 parameter
  - Event that will be generated on selected Trap OID
  - User Tag is special event attribute, that can be got by %u macros or as attribute
    of event class. This attribute can be set there or by script.
  - Parameters - OID values that will be passed to event as $2, $3, $4... parameters

In parameter configuration(:guilabel:`Edit SNMP Trap Parameter Mapping`) can be
configured next things:

  - Description of a parameter
  - Select if parameter should be found by OID or by position in the message
  - Option not to convert value to hex string. If string contains not readable
    symbols(symbol number less than space symbol number) it will be automatically
    converted to hex string, this option is required to prevent auto conversion.

.. figure:: _images/snmp_trap_mapping_configuration.png

.. _default_snmp:

Default SNMP credentials
========================

Default SNMP credentials can be set in :menuselection:`Configuration --> SNMP Credentials`.
It does not matter if credentials are used for adding nodes manually, through network
discovery or with the help of agent registration - in each case :guilabel:`SNMP Credentials`
configuration value will be checked.

.. figure:: _images/snmp_credentials.png


Using ifTable and ifXTable
==========================

There are 2 types of subtree that provides information about interfaces: old one
ifTable and new one ifXTable. Sometimes usage of new one creates error situations.
In this situation ifXTable can be disabled. This can be done in Properties of
:term:`node <Node>` in :guilabel:`Polling`. Or this configuration can be set
globally by changing ``UseIfXTable`` server configuration parameter.

.. figure:: _images/node_polling_tab.png

Configure SNMP Proxy
====================

If there is need to monitor nodes behind firewall using SNMP, there is option
to install on one of the nodes |product_name| agent, open all required ports for this node
and send SNMP request to other nodes in this subnet through installed agent.

Proxy configuration can be done wile creation of node of for already created node
can be change in :guilabel:`Communications` tab of node properties. To configure
proxy node select node in object selector :guilabel:`SNMP Proxy`.

.. figure:: _images/create_node.png


.. figure:: _images/node_communications_tab.png

Agent configuration
-------------------

To enable SNMP proxy "EnableSNMPProxy" parameter should be set to "yes".


Configure SNMP Trap Proxy
=========================

It is possible to proxy SNMP traps.

In this case as a destination of traps should be set the proxy node.

Agent configuration
-------------------

To enable trap proxy "EnableSNMPTrapProxy" parameter should be set to "yes".

Optionally can be configured also "SNMPTrapListenAddress" and "SNMPTrapPort".
Default values can be checked there: :ref:`master-configuration-file-label`

Server configuration
--------------------

By default traps are accepted only from known nodes. To accept all traps
set "LogAllSNMPTraps" server configuration variable to 1.

To correctly send response for SNMPv3, it should be also configured
the proxy node for the sender node. It is done in sender node
properties in "Communications" tab, SNMP section.

.. _import-mib:

Import MIB
==========

MIB files (MIBs) describe structure of information transferred via SNMP.
Every device can support multiple MIBs, some of them are standard and
public, other can be proprietary and vendor specific. |product_name| uses compiled
MIBs to allow you to select OID and see its description (for example when
selecting SNMP data for DCI collection). You do not need to compile new
MIBs if you are OK with direct input of OID.

Manage User MIBs
----------------

To add additional MIBs go to :guilabel:`Configuration` --> :guilabel:`SNMP MIB files`. 
Upload mib files with extension **.mib** and hit compile button. MIB compilation log will 
be visible in :guilabel:`Output` tab and warnings/errors will be added to 
:guilabel:`Error Log` tab. Afret MIB files are succesfully compiled all opened clients  
automatically dowload new version form server. 

.. figure:: _images/SNMP_MIB_files.png

For versions older 5.0
----------------------

Compiling MIBs
~~~~~~~~~~~~~~

 - Change suffix of your new MIB file to .txt
 - Copy your MIB file to /usr/share/netxms/mibs
 - Use nxmibc binary to create a new compiled MIB file from all MIBs in directory.
   Add parameter -z for compressed output file.

.. code-block:: shell

  nxmibc -d /usr/share/netxms/mibs -o /var/lib/netxms/netxms.mib

Parameters recognized by nxmibc:

.. code-block:: shell

  nxmibc [options] source1 ... sourceN

  Valid options:
    -d <dir>  : Include all MIB files from given directory to compilation
    -o <file> : Set output file name (default is netxms.mib)
    -P        : Pause before exit
    -s        : Strip descriptions from MIB objects
    -z        : Compress output file

Troubleshooting
~~~~~~~~~~~~~~~

If nxmibc fails, it may be caused by syntax or import errors in your MIB.
Try to check it with smilint (part of net-snmp package) and correct any
errors on level 3.

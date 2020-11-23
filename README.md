![GitHub](https://img.shields.io/github/license/ksator/gnmi_demo_with_arista_eos)

# Table of content

[About this repository](#about-this-repository)   
[Data models on Arista EOS](#data-models-on-arista-eos)   
[pyang](#pyang)   
&nbsp;&nbsp;&nbsp;&nbsp;[About pyang](#about-pyang)   
&nbsp;&nbsp;&nbsp;&nbsp;[yang modules validation](#yang-modules-validation)   
&nbsp;&nbsp;&nbsp;&nbsp;[convert a YANG module into an equivalent YIN module](#convert-a-yang-module-into-an-equivalent-yin-module)  
&nbsp;&nbsp;&nbsp;&nbsp;[Generate a tree representation of YANG modules for quick visualization](#generate-a-tree-representation-of-yang-modules-for-quick-visualization)   
[pyangbind](#pyangbind)   
&nbsp;&nbsp;&nbsp;&nbsp;[About pyangbind](#about-pyangbind)   
&nbsp;&nbsp;&nbsp;&nbsp;[pyangbind installation](#pyangbind-installation)   
&nbsp;&nbsp;&nbsp;&nbsp;[converts a YANG module into a Python module](#converts-a-yang-module-into-a-python-module)        
&nbsp;&nbsp;&nbsp;&nbsp;[Use the python module to generate data](#use-the-python-module-to-generate-data)   
[gNMI (gRPC Network Management Interface)](#gnmi-grpc-network-management-interface)   
&nbsp;&nbsp;&nbsp;&nbsp;[requirements on Arista devices](#requirements-on-arista-devices)   
&nbsp;&nbsp;&nbsp;&nbsp;[install the gNMI command-line client](#install-the-gnmi-command-line-client)   
&nbsp;&nbsp;&nbsp;&nbsp;[interact with Arista devices using gNMI](#interact-with-arista-devices-using-gnmi)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Capabilities RPC](#capabilities-rpc)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Subscribe RPC](#subscribe-rpc)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Get RPC](#get-rpc)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Set RPC](#set-rpc)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[OpenConfig paths and EOS native paths](#openconfig-paths-and-eos-native-paths)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[device configuration end to end demo](device-configuration-end-to-end-demo)



# About this repository 

We will use pyang, pyangbind, and gNMI.  

We will interact with Arista EOS devices using gNMI.   
We will use a gnmi command-line client and the following RPC: capabilites, get, subscribe, set. 
# Data models on Arista EOS

They are published on this repository  https://github.com/aristanetworks/yang 

# pyang 

## About pyang 

pyang is a python program.  
We can use it to:
- Validate YANG modules against YANG RFCs
- Convert YANG modules into equivalent YIN module (XML)
- Generate a tree representation of YANG models for quick visualization 

## pyang installation

```
python -V              
Python 3.7.7
```

pyang installation from pypi 
```
$ pip install pyang
```

```
pip freeze | grep pyang
pyang==2.2.1
```

## yang modules validation

Example: let's validate the modules "openconfig-bgp.yang" and "openconfig-interfaces.yang" from [openconfig github repository](https://github.com/openconfig/public.git)


### clone the openconfig repository
```
git clone https://github.com/openconfig/public.git
ls public
```

### move all the yang files from Openconfig to the same directory

The YANG modules openconfig-bgp.yang and openconfig-interfaces.yang import other YANG modules: so we need to make sure pyang can find all these yang modules 

move all the yang files to the directory yang_modules
```
mkdir yang_modules
cp public/release/models/*.yang yang_modules/.
cp -R public/release/models/*/*.yang yang_modules/.
```
```
ls yang_modules
cd yang_modules
```

### validate yang modules 

```
pyang openconfig-bgp.yang
pyang openconfig-interfaces.yang 
```

## convert a YANG module into an equivalent YIN module

A YANG module can be translated into an XML syntax called YIN. The translated module is called a YIN module. The YANG and YIN formats contain equivalent information using different notations: YIN is YANG in XML. A YANG module can be translated into YIN syntax without losing any information.  

Example (openconfig-bgp.yin is the YIN equivalent of openconfig-bgp.yang)
```
pyang openconfig-bgp.yang -f yin -o openconfig-bgp.yin
ls *.yin
```

## Generate a tree representation of YANG modules for quick visualization

```
pyang -f tree openconfig-interfaces.yang                                        
```
<details><summary>click me to see the output</summary>
<p>

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface* [name]
        +--rw name             -> ../config/name
        +--rw config
        |  +--rw name?            string
        |  +--rw type             identityref
        |  +--rw mtu?             uint16
        |  +--rw loopback-mode?   boolean
        |  +--rw description?     string
        |  +--rw enabled?         boolean
        +--ro state
        |  +--ro name?            string
        |  +--ro type             identityref
        |  +--ro mtu?             uint16
        |  +--ro loopback-mode?   boolean
        |  +--ro description?     string
        |  +--ro enabled?         boolean
        |  +--ro ifindex?         uint32
        |  +--ro admin-status     enumeration
        |  +--ro oper-status      enumeration
        |  +--ro last-change?     oc-types:timeticks64
        |  +--ro logical?         boolean
        |  +--ro counters
        |     +--ro in-octets?             oc-yang:counter64
        |     +--ro in-pkts?               oc-yang:counter64
        |     +--ro in-unicast-pkts?       oc-yang:counter64
        |     +--ro in-broadcast-pkts?     oc-yang:counter64
        |     +--ro in-multicast-pkts?     oc-yang:counter64
        |     +--ro in-discards?           oc-yang:counter64
        |     +--ro in-errors?             oc-yang:counter64
        |     +--ro in-unknown-protos?     oc-yang:counter64
        |     +--ro in-fcs-errors?         oc-yang:counter64
        |     +--ro out-octets?            oc-yang:counter64
        |     +--ro out-pkts?              oc-yang:counter64
        |     +--ro out-unicast-pkts?      oc-yang:counter64
        |     +--ro out-broadcast-pkts?    oc-yang:counter64
        |     +--ro out-multicast-pkts?    oc-yang:counter64
        |     +--ro out-discards?          oc-yang:counter64
        |     +--ro out-errors?            oc-yang:counter64
        |     +--ro carrier-transitions?   oc-yang:counter64
        |     +--ro last-clear?            oc-types:timeticks64
        +--rw hold-time
        |  +--rw config
        |  |  +--rw up?     uint32
        |  |  +--rw down?   uint32
        |  +--ro state
        |     +--ro up?     uint32
        |     +--ro down?   uint32
        +--rw subinterfaces
           +--rw subinterface* [index]
              +--rw index     -> ../config/index
              +--rw config
              |  +--rw index?         uint32
              |  +--rw description?   string
              |  +--rw enabled?       boolean
              +--ro state
                 +--ro index?          uint32
                 +--ro description?    string
                 +--ro enabled?        boolean
                 +--ro name?           string
                 +--ro ifindex?        uint32
                 +--ro admin-status    enumeration
                 +--ro oper-status     enumeration
                 +--ro last-change?    oc-types:timeticks64
                 +--ro logical?        boolean
                 +--ro counters
                    +--ro in-octets?             oc-yang:counter64
                    +--ro in-pkts?               oc-yang:counter64
                    +--ro in-unicast-pkts?       oc-yang:counter64
                    +--ro in-broadcast-pkts?     oc-yang:counter64
                    +--ro in-multicast-pkts?     oc-yang:counter64
                    +--ro in-discards?           oc-yang:counter64
                    +--ro in-errors?             oc-yang:counter64
                    +--ro in-unknown-protos?     oc-yang:counter64
                    +--ro in-fcs-errors?         oc-yang:counter64
                    +--ro out-octets?            oc-yang:counter64
                    +--ro out-pkts?              oc-yang:counter64
                    +--ro out-unicast-pkts?      oc-yang:counter64
                    +--ro out-broadcast-pkts?    oc-yang:counter64
                    +--ro out-multicast-pkts?    oc-yang:counter64
                    +--ro out-discards?          oc-yang:counter64
                    +--ro out-errors?            oc-yang:counter64
                    +--ro carrier-transitions?   oc-yang:counter64
                    +--ro last-clear?            oc-types:timeticks64
```
</p>
</details>


```
pyang openconfig-interfaces.yang -f tree --tree-path=/interfaces/interface/state       
```
<details><summary>click me to see the output</summary>
<p>

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface* [name]
        +--ro state
           +--ro name?            string
           +--ro type             identityref
           +--ro mtu?             uint16
           +--ro loopback-mode?   boolean
           +--ro description?     string
           +--ro enabled?         boolean
           +--ro ifindex?         uint32
           +--ro admin-status     enumeration
           +--ro oper-status      enumeration
           +--ro last-change?     oc-types:timeticks64
           +--ro logical?         boolean
           +--ro counters
              +--ro in-octets?             oc-yang:counter64
              +--ro in-pkts?               oc-yang:counter64
              +--ro in-unicast-pkts?       oc-yang:counter64
              +--ro in-broadcast-pkts?     oc-yang:counter64
              +--ro in-multicast-pkts?     oc-yang:counter64
              +--ro in-discards?           oc-yang:counter64
              +--ro in-errors?             oc-yang:counter64
              +--ro in-unknown-protos?     oc-yang:counter64
              +--ro in-fcs-errors?         oc-yang:counter64
              +--ro out-octets?            oc-yang:counter64
              +--ro out-pkts?              oc-yang:counter64
              +--ro out-unicast-pkts?      oc-yang:counter64
              +--ro out-broadcast-pkts?    oc-yang:counter64
              +--ro out-multicast-pkts?    oc-yang:counter64
              +--ro out-discards?          oc-yang:counter64
              +--ro out-errors?            oc-yang:counter64
              +--ro carrier-transitions?   oc-yang:counter64
              +--ro last-clear?            oc-types:timeticks64
```
</p>
</details>

```
pyang openconfig-interfaces.yang -f tree  --tree-depth=4  
```
<details><summary>click me to see the output</summary>
<p>

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface* [name]
        +--rw name             -> ../config/name
        +--rw config
        |  +--rw name?            string
        |  +--rw type             identityref
        |  +--rw mtu?             uint16
        |  +--rw loopback-mode?   boolean
        |  +--rw description?     string
        |  +--rw enabled?         boolean
        +--ro state
        |  +--ro name?            string
        |  +--ro type             identityref
        |  +--ro mtu?             uint16
        |  +--ro loopback-mode?   boolean
        |  +--ro description?     string
        |  +--ro enabled?         boolean
        |  +--ro ifindex?         uint32
        |  +--ro admin-status     enumeration
        |  +--ro oper-status      enumeration
        |  +--ro last-change?     oc-types:timeticks64
        |  +--ro logical?         boolean
        |  +--ro counters
        |        ...
        +--rw hold-time
        |  +--rw config
        |  |     ...
        |  +--ro state
        |        ...
        +--rw subinterfaces
           +--rw subinterface* [index]
                 ...
```
</p>
</details>

# pyangbind 

## About pyangbind 

pyangbind is a pyang plugin.  
It generates Python classes from a YANG module: It converts  YANG module into a Python module, such that Python can be used to generate data which conforms with the data model defined in YANG.

## pyangbind installation

```
python -V              
Python 3.7.7
```
pyangbind installation from pypi 
```
$ pip install pyangbind
```
```
pip freeze | grep pyang
pyang==2.2.1
pyangbind==0.8.1
```

## converts a YANG module into a Python module

Example with the yang module openconfig-bgp.yang 
```
pyang --plugindir $VIRTUAL_ENV/lib/python3.7/site-packages/pyangbind/plugin -f pybind -o oc_bgp.py openconfig-bgp.yang
```
We have now a python module oc_bgp.py
```
ls | grep oc_bgp.py
```

## Use the python module to generate data  

```
Python 3.7.7 (default, Mar 10 2020, 15:43:33) 
[Clang 11.0.0 (clang-1100.0.33.17)] on darwin
Type "help", "copyright", "credits" or "license" for more information. 
>>> 
>>> from oc_bgp import openconfig_bgp
>>> from json import dumps
>>> oc=openconfig_bgp()
>>> oc.bgp.peer_groups.peer_group.add("OC")
<pyangbind.lib.yangtypes.YANGDynClass.<locals>.YANGBaseClass object at 0x106e55310>
>>> oc.bgp.peer_groups.peer_group["OC"].config.local_as=104
>>> #oc.bgp.peer_groups.peer_group["OC"].config.peer_type="EXTERNAL"
>>> #oc.bgp.peer_groups.peer_group["OC"].apply_policy.config.import_policy="bgp_in"
>>> #oc.bgp.peer_groups.peer_group["OC"].apply_policy.config.export_policy="bgp_out"
>>> oc.bgp.neighbors.neighbor.add("192.168.1.2")
<pyangbind.lib.yangtypes.YANGDynClass.<locals>.YANGBaseClass object at 0x106e55470>
>>> oc.bgp.neighbors.neighbor.add("192.168.1.22")
<pyangbind.lib.yangtypes.YANGDynClass.<locals>.YANGBaseClass object at 0x1074301b0>
>>> oc.bgp.neighbors.neighbor["192.168.1.2"].config.peer_as="110"
>>> oc.bgp.neighbors.neighbor["192.168.1.2"].config.peer_group="OC"
>>> oc.bgp.neighbors.neighbor["192.168.1.22"].config.peer_as="120"
>>> oc.bgp.neighbors.neighbor["192.168.1.22"].config.peer_group="OC"
```
```
>>> print(oc.bgp.neighbors.neighbor["192.168.1.22"].config.peer_as)
120
```

```
>>> oc.get(filter=True)
{'bgp': {'neighbors': {'neighbor': OrderedDict([('192.168.1.2', {'neighbor-address': '192.168.1.2', 'config': {'peer-group': 'OC', 'peer-as': 110}}), ('192.168.1.22', {'neighbor-address': '192.168.1.22', 'config': {'peer-group': 'OC', 'peer-as': 120}})])}, 'peer-groups': {'peer-group': OrderedDict([('OC', {'peer-group-name': 'OC', 'config': {'local-as': 104}})])}}}
>>> dumps(oc.get(filter=True))
'{"bgp": {"neighbors": {"neighbor": {"192.168.1.2": {"neighbor-address": "192.168.1.2", "config": {"peer-group": "OC", "peer-as": 110}}, "192.168.1.22": {"neighbor-address": "192.168.1.22", "config": {"peer-group": "OC", "peer-as": 120}}}}, "peer-groups": {"peer-group": {"OC": {"peer-group-name": "OC", "config": {"local-as": 104}}}}}}'
>>> print(dumps(oc.get(filter=True), indent=4))
{
    "bgp": {
        "neighbors": {
            "neighbor": {
                "192.168.1.2": {
                    "neighbor-address": "192.168.1.2",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 110
                    }
                },
                "192.168.1.22": {
                    "neighbor-address": "192.168.1.22",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 120
                    }
                }
            }
        },
        "peer-groups": {
            "peer-group": {
                "OC": {
                    "peer-group-name": "OC",
                    "config": {
                        "local-as": 104
                    }
                }
            }
        }
    }
}
>>> 
```
Default format 
```
>>> import pyangbind.lib.pybindJSON as pybindJSON
>>> print(pybindJSON.dumps(oc))
{
    "bgp": {
        "neighbors": {
            "neighbor": {
                "192.168.1.2": {
                    "neighbor-address": "192.168.1.2",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 110
                    }
                },
                "192.168.1.22": {
                    "neighbor-address": "192.168.1.22",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 120
                    }
                }
            }
        },
        "peer-groups": {
            "peer-group": {
                "OC": {
                    "peer-group-name": "OC",
                    "config": {
                        "local-as": 104
                    }
                }
            }
        }
    }
}
>>> 
```
[rfc 7951](https://tools.ietf.org/html/rfc7951) format 
```
>>> print(pybindJSON.dumps(oc, mode="ietf"))
{
    "openconfig-bgp:bgp": {
        "neighbors": {
            "neighbor": [
                {
                    "neighbor-address": "192.168.1.2",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 110
                    }
                },
                {
                    "neighbor-address": "192.168.1.22",
                    "config": {
                        "peer-group": "OC",
                        "peer-as": 120
                    }
                }
            ]
        },
        "peer-groups": {
            "peer-group": [
                {
                    "peer-group-name": "OC",
                    "config": {
                        "local-as": 104
                    }
                }
            ]
        }
    }
}
>>> 

>>> print(pybindJSON.dumps(oc.bgp.neighbors, mode="ietf"))
{
    "openconfig-bgp:neighbor": [
        {
            "neighbor-address": "192.168.1.2",
            "config": {
                "peer-group": "OC",
                "peer-as": 110
            }
        },
        {
            "neighbor-address": "192.168.1.22",
            "config": {
                "peer-group": "OC",
                "peer-as": 120
            }
        }
    ]
}
>>> 
```
# gNMI (gRPC Network Management Interface)

We will use [this gnmi command-line client](https://github.com/aristanetworks/goarista/tree/master/cmd/gnmi) and the following RPC: capabilites, get, subscribe, set. 

## requirements on Arista devices 

```
username arista secret 0 arista
ip access-list GNMI
  10 permit tcp any any eq gnmi
management api gnmi
  transport grpc def
    ip access-group GNMI
  provider eos-native
```

`provider eos-native` is required to serve gNMI subscription/get requests with EOS native paths.  
So, using the above configuration, a gNMI client can subscribes to both OpenConfig paths and native paths.

```
show management api gnmi 
```

## install the gNMI command-line client 

install Go
```
go version
go version go1.14.3 darwin/amd64
```

install [this gnmi command-line client](https://github.com/aristanetworks/goarista/tree/master/cmd/gnmi) 
```
ls $HOME/Go/bin | grep gnmi
gnmi                                                   
```
```
./gnmi --help
```

### interact with Arista devices using gNMI 

Lets use the following RPC: capabilites, get, subscribe, set. 

#### Capabilities RPC

Retrieve the set of capabilities supported by the target. 

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista capabilities
```
<details><summary>click me to see the output</summary>
<p>

```
Version: 0.7.0
SupportedModel: name:"openconfig-ospf-types" organization:"OpenConfig working group" version:"0.1.3" 
SupportedModel: name:"openconfig-types" organization:"OpenConfig working group" version:"0.5.1" 
SupportedModel: name:"arista-lldp-augments" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-lldp" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-mpls-types" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"arista-rpol-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-isis-augments" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-system-logging" organization:"OpenConfig working group" version:"0.3.1" 
SupportedModel: name:"openconfig-vlan" organization:"OpenConfig working group" version:"3.0.2" 
SupportedModel: name:"openconfig-bgp" organization:"OpenConfig working group" version:"5.0.2" 
SupportedModel: name:"openconfig-mpls-sr" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"openconfig-ospfv2" organization:"OpenConfig working group" version:"0.1.3" 
SupportedModel: name:"openconfig-aft-types" organization:"OpenConfig Working Group" version:"0.3.2" 
SupportedModel: name:"openconfig-network-instance-types" organization:"OpenConfig working group" version:"0.8.2" 
SupportedModel: name:"openconfig-if-ip" organization:"OpenConfig working group" version:"2.3.1" 
SupportedModel: name:"arista-bgp-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-vlan-deviations" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-aaa-types" organization:"OpenConfig working group" version:"0.4.1" 
SupportedModel: name:"vlan-translation" organization:"Arista Networks" 
SupportedModel: name:"arista-gnoi-cert" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-system-terminal" organization:"OpenConfig working group" version:"0.3.1" 
SupportedModel: name:"ietf-interfaces" organization:"IETF NETMOD (Network Modeling) Working Group" 
SupportedModel: name:"arista-lldp-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-bfd-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-netinst-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-exp-eos-qos-acl-config" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-mpls" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"arista-pim-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-intf-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-system-management" organization:"OpenConfig working group" version:"0.1.2" 
SupportedModel: name:"openconfig-platform-fan" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-mpls-ldp" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"openconfig-platform-psu" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-platform-linecard" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-system" organization:"OpenConfig working group" version:"0.6.1" 
SupportedModel: name:"openconfig-policy-forwarding" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-igmp" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"arista-exp-eos-mlag" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-bfd-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-system-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-lacp-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-alarm-types" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-bgp-policy" organization:"OpenConfig working group" version:"5.0.2" 
SupportedModel: name:"openconfig-isis-lsdb-types" organization:"OpenConfig working group" version:"0.4.2" 
SupportedModel: name:"arista-lacp-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-exp-eos-multicast" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-isis-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-aft" organization:"OpenConfig working group" version:"0.3.2" 
SupportedModel: name:"openconfig-yang-types" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"ietf-netconf-monitoring" organization:"IETF NETCONF (Network Configuration) Working Group" 
SupportedModel: name:"openconfig-if-ethernet" organization:"OpenConfig working group" version:"2.6.2" 
SupportedModel: name:"openconfig-platform-types" organization:"OpenConfig working group" version:"0.10.1" 
SupportedModel: name:"openconfig-lacp" organization:"OpenConfig working group" version:"1.1.1" 
SupportedModel: name:"openconfig-vlan-types" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"openconfig-bgp-types" organization:"OpenConfig working group" version:"5.0.2" 
SupportedModel: name:"openconfig-if-aggregate" organization:"OpenConfig working group" version:"2.3.2" 
SupportedModel: name:"openconfig-pim" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-qos" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-procmon" organization:"OpenConfig working group" version:"0.3.1" 
SupportedModel: name:"openconfig-ospf-policy" organization:"OpenConfig working group" version:"0.1.3" 
SupportedModel: name:"arista-relay-agent-deviations" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-alarms" organization:"OpenConfig working group" version:"0.3.1" 
SupportedModel: name:"arista-rpol-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-system-augments" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-exp-eos-vxlan" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-rpc-netconf" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-exp-eos-igmpsnooping" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-intf-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-vlan-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-isis-types" organization:"OpenConfig working group" version:"0.4.2" 
SupportedModel: name:"openconfig-network-instance" organization:"OpenConfig working group" version:"0.11.1" 
SupportedModel: name:"openconfig-isis" organization:"OpenConfig working group" version:"0.4.2" 
SupportedModel: name:"openconfig-bfd" organization:"OpenConfig working group" version:"0.1.0" 
SupportedModel: name:"arista-srte-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"iana-if-type" organization:"IANA" 
SupportedModel: name:"openconfig-if-types" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-packet-match-types" organization:"OpenConfig working group" version:"1.0.2" 
SupportedModel: name:"openconfig-qos-types" organization:"OpenConfig working group" version:"0.2.1" 
SupportedModel: name:"openconfig-policy-types" organization:"OpenConfig working group" version:"3.1.1" 
SupportedModel: name:"arista-exp-eos" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-interfaces" organization:"OpenConfig working group" version:"2.4.1" 
SupportedModel: name:"openconfig-network-instance-l3" organization:"OpenConfig working group" version:"0.11.1" 
SupportedModel: name:"openconfig-routing-policy" organization:"OpenConfig working group" version:"3.1.1" 
SupportedModel: name:"arista-mpls-augments" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"ietf-yang-types" organization:"IETF NETMOD (NETCONF Data Modeling Language) Working Group" 
SupportedModel: name:"openconfig-packet-match" organization:"OpenConfig working group" version:"1.1.1" 
SupportedModel: name:"openconfig-local-routing" organization:"OpenConfig working group" version:"1.0.2" 
SupportedModel: name:"ietf-netconf" organization:"IETF NETCONF (Network Configuration) Working Group" 
SupportedModel: name:"openconfig-platform" organization:"OpenConfig working group" version:"0.12.1" 
SupportedModel: name:"arista-acl-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"arista-exp-eos-qos" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"arista-mpls-deviations" organization:"Arista Networks, Inc." 
SupportedModel: name:"openconfig-acl" organization:"OpenConfig working group" version:"1.0.2" 
SupportedModel: name:"openconfig-segment-routing" organization:"OpenConfig working group" version:"0.0.4" 
SupportedModel: name:"arista-exp-eos-qos-config" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-aaa" organization:"OpenConfig working group" version:"0.4.1" 
SupportedModel: name:"openconfig-pim-types" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"arista-exp-eos-vxlan-config" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"ietf-inet-types" organization:"IETF NETMOD (NETCONF Data Modeling Language) Working Group" 
SupportedModel: name:"openconfig-hercules-interfaces" organization:"OpenConfig Hercules Working Group" version:"0.2.0" 
SupportedModel: name:"arista-exp-eos-varp" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-mpls-rsvp" organization:"OpenConfig working group" version:"3.0.1" 
SupportedModel: name:"openconfig-pf-srte" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-extensions" organization:"OpenConfig working group" 
SupportedModel: name:"openconfig-igmp-types" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-relay-agent" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-if-tunnel" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"openconfig-inet-types" organization:"OpenConfig working group" version:"0.3.2" 
SupportedModel: name:"arista-eos-types" organization:"Arista Networks <http://arista.com/>" 
SupportedModel: name:"openconfig-lldp-types" organization:"OpenConfig working group" version:"0.1.1" 
SupportedModel: name:"arista-local-routing-deviations" organization:"Arista Networks, Inc." 
SupportedEncoding: JSON
SupportedEncoding: JSON_IETF
SupportedEncoding: ASCII
```
</p>
</details>


#### Subscribe RPC

Request to the target to stream values for a path

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista subscribe '/network-instances/network-instance/protocols/protocol/bgp/neighbors/neighbor/state'
```
<details><summary>click me to see the output</summary>
<p>

```
[2020-05-31T22:21:31.707253093Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/dynamically-configured = false
[2020-05-31T22:21:31.707256177Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/enabled = true
[2020-05-31T22:21:31.741369402Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/established-transitions = 1
[2020-05-31T22:21:31.741756411Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/last-established = 159096348629
[2020-05-31T22:21:31.711484298Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/neighbor-address = 10.10.10.0
[2020-05-31T22:21:31.718742166Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/peer-as = 65001
[2020-05-31T22:21:31.70725986Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/route-flap-damping = false
[2020-05-31T22:21:31.707263783Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/send-community = NONE
[2020-05-31T22:21:31.741504683Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.0]/state/session-state = ESTABLISHED
[2020-05-31T22:21:31.711775449Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/dynamically-configured = false
[2020-05-31T22:21:31.711778386Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/enabled = true
[2020-05-31T22:21:31.742386216Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/established-transitions = 1
[2020-05-31T22:21:31.74248292Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/last-established = 159096348429
[2020-05-31T22:21:31.712592538Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/neighbor-address = 10.10.10.5
[2020-05-31T22:21:31.730382272Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/peer-as = 65003
[2020-05-31T22:21:31.711782096Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/route-flap-damping = false
[2020-05-31T22:21:31.711786244Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/send-community = NONE
[2020-05-31T22:21:31.742155428Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/session-state = ESTABLISHED
```
</p>
</details>

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista subscribe '/network-instances/network-instance/protocols/protocol/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state'
```
<details><summary>click me to see the output</summary>
<p>


```
[2020-05-31T22:21:31.711775449Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/dynamically-configured = false
[2020-05-31T22:21:31.711778386Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/enabled = true
[2020-05-31T22:21:31.742386216Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/established-transitions = 1
[2020-05-31T22:21:31.74248292Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/last-established = 159096348429
[2020-05-31T22:21:31.712592538Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/neighbor-address = 10.10.10.5
[2020-05-31T22:21:31.730382272Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/peer-as = 65003
[2020-05-31T22:21:31.711782096Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/route-flap-damping = false
[2020-05-31T22:21:31.711786244Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/send-community = NONE
[2020-05-31T22:21:31.742155428Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/state/session-state = ESTABLISHED
```
</p>
</details>

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista subscribe '/network-instances/network-instance/protocols/protocol/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config'
```
<details><summary>click me to see the output</summary>
<p>

```
[2020-05-31T23:02:35.055278732Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config/enabled = true
[2020-05-31T23:02:35.055824489Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config/neighbor-address = 10.10.10.5
[2020-05-31T23:02:35.063057098Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config/peer-as = 65003
[2020-05-31T23:02:35.05528639Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config/route-flap-damping = false
[2020-05-31T23:02:35.055282396Z] /network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.5]/config/send-community = NONE
```
</p>
</details>

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista subscribe '/interfaces/interface[name=Ethernet24]/state/counters'
```
<details><summary>click me to see the output</summary>
<p>

```
[2020-05-31T22:21:31.317592414Z] /interfaces/interface[name=Ethernet24]/state/counters/in-broadcast-pkts = 0
[2020-05-31T22:21:31.317572819Z] /interfaces/interface[name=Ethernet24]/state/counters/in-discards = 0
[2020-05-31T22:21:31.317667791Z] /interfaces/interface[name=Ethernet24]/state/counters/in-errors = 0
[2020-05-31T22:29:55.324957018Z] /interfaces/interface[name=Ethernet24]/state/counters/in-multicast-pkts = 19
[2020-05-31T22:29:55.324938409Z] /interfaces/interface[name=Ethernet24]/state/counters/in-octets = 6637
[2020-05-31T22:29:23.307923971Z] /interfaces/interface[name=Ethernet24]/state/counters/in-unicast-pkts = 28
[2020-05-31T22:21:31.317558268Z] /interfaces/interface[name=Ethernet24]/state/counters/out-broadcast-pkts = 1
[2020-05-31T22:21:31.317641085Z] /interfaces/interface[name=Ethernet24]/state/counters/out-discards = 0
[2020-05-31T22:21:31.317650693Z] /interfaces/interface[name=Ethernet24]/state/counters/out-errors = 0
[2020-05-31T22:29:53.324273922Z] /interfaces/interface[name=Ethernet24]/state/counters/out-multicast-pkts = 18
[2020-05-31T22:29:53.324253853Z] /interfaces/interface[name=Ethernet24]/state/counters/out-octets = 5865
[2020-05-31T22:29:23.307958857Z] /interfaces/interface[name=Ethernet24]/state/counters/out-unicast-pkts = 19
[2020-05-31T22:30:23.3394854Z] /interfaces/interface[name=Ethernet24]/state/counters/out-multicast-pkts = 19
[2020-05-31T22:30:23.339507569Z] /interfaces/interface[name=Ethernet24]/state/counters/in-unicast-pkts = 30
[2020-05-31T22:30:23.339558179Z] /interfaces/interface[name=Ethernet24]/state/counters/in-octets = 6796
[2020-05-31T22:30:23.340718713Z] /interfaces/interface[name=Ethernet24]/state/counters/out-octets = 6250
[2020-05-31T22:30:23.340740613Z] /interfaces/interface[name=Ethernet24]/state/counters/out-unicast-pkts = 21
[2020-05-31T22:30:25.34256155Z] /interfaces/interface[name=Ethernet24]/state/counters/in-octets = 7022
[2020-05-31T22:30:25.342580956Z] /interfaces/interface[name=Ethernet24]/state/counters/in-multicast-pkts = 20
```
</p>
</details>

#### Get RPC

Retrieve a snapshot for a path 
```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista get '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors'
```
<details><summary>click me to see the output</summary>
<p>
  
```

/network-instances/network-instance[name=default]/protocols/protocol[identifier=BGP][name=BGP]/bgp/neighbors:
{
  "openconfig-network-instance:neighbor": [
    {
      "afi-safis": {
        "afi-safi": [
          {
            "add-paths": {
              "config": {
                "send": false
              },
              "state": {
                "send": false
              }
            },
            "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST",
            "apply-policy": {
              "config": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              },
              "state": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              }
            },
            "config": {
              "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST"
            },
            "graceful-restart": {
              "config": {},
              "state": {}
            },
            "state": {
              "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST"
            }
          },
          {
            "add-paths": {
              "config": {
                "send": false
              },
              "state": {
                "send": false
              }
            },
            "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST",
            "apply-policy": {
              "config": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              },
              "state": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              }
            },
            "config": {
              "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST"
            },
            "graceful-restart": {
              "config": {},
              "state": {}
            },
            "state": {
              "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST"
            }
          }
        ]
      },
      "apply-policy": {
        "config": {
          "default-export-policy": "REJECT_ROUTE",
          "default-import-policy": "REJECT_ROUTE"
        },
        "state": {
          "default-export-policy": "REJECT_ROUTE",
          "default-import-policy": "REJECT_ROUTE"
        }
      },
      "as-path-options": {
        "config": {
          "disable-peer-as-filter": false,
          "replace-peer-as": false
        },
        "state": {
          "disable-peer-as-filter": false,
          "replace-peer-as": false
        }
      },
      "config": {
        "enabled": true,
        "neighbor-address": "10.10.10.0",
        "peer-as": 65001,
        "route-flap-damping": false,
        "send-community": "NONE"
      },
      "ebgp-multihop": {
        "config": {
          "enabled": false,
          "multihop-ttl": 0
        },
        "state": {
          "enabled": false,
          "multihop-ttl": 0
        }
      },
      "error-handling": {
        "config": {
          "treat-as-withdraw": false
        },
        "state": {
          "treat-as-withdraw": false
        }
      },
      "neighbor-address": "10.10.10.0",
      "route-reflector": {
        "config": {},
        "state": {}
      },
      "state": {
        "dynamically-configured": false,
        "enabled": true,
        "established-transitions": "1",
        "last-established": "159096348629",
        "neighbor-address": "10.10.10.0",
        "peer-as": 65001,
        "route-flap-damping": false,
        "send-community": "NONE",
        "session-state": "ESTABLISHED"
      },
      "timers": {
        "config": {
          "connect-retry": "30.0",
          "minimum-advertisement-interval": "30.0"
        },
        "state": {
          "connect-retry": "30.0",
          "minimum-advertisement-interval": "30.0"
        }
      },
      "transport": {
        "config": {
          "mtu-discovery": true
        },
        "state": {
          "mtu-discovery": true,
          "remote-address": "10.10.10.0",
          "remote-port": 0
        }
      },
      "use-multiple-paths": {
        "config": {
          "enabled": false
        },
        "ebgp": {
          "config": {
            "allow-multiple-as": false
          },
          "state": {
            "allow-multiple-as": false
          }
        },
        "state": {
          "enabled": false
        }
      }
    },
    {
      "afi-safis": {
        "afi-safi": [
          {
            "add-paths": {
              "config": {
                "send": false
              },
              "state": {
                "send": false
              }
            },
            "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST",
            "apply-policy": {
              "config": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              },
              "state": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              }
            },
            "config": {
              "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST"
            },
            "graceful-restart": {
              "config": {},
              "state": {}
            },
            "state": {
              "afi-safi-name": "openconfig-bgp-types:IPV6_UNICAST"
            }
          },
          {
            "add-paths": {
              "config": {
                "send": false
              },
              "state": {
                "send": false
              }
            },
            "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST",
            "apply-policy": {
              "config": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              },
              "state": {
                "default-export-policy": "REJECT_ROUTE",
                "default-import-policy": "REJECT_ROUTE"
              }
            },
            "config": {
              "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST"
            },
            "graceful-restart": {
              "config": {},
              "state": {}
            },
            "state": {
              "afi-safi-name": "openconfig-bgp-types:IPV4_UNICAST"
            }
          }
        ]
      },
      "apply-policy": {
        "config": {
          "default-export-policy": "REJECT_ROUTE",
          "default-import-policy": "REJECT_ROUTE"
        },
        "state": {
          "default-export-policy": "REJECT_ROUTE",
          "default-import-policy": "REJECT_ROUTE"
        }
      },
      "as-path-options": {
        "config": {
          "disable-peer-as-filter": false,
          "replace-peer-as": false
        },
        "state": {
          "disable-peer-as-filter": false,
          "replace-peer-as": false
        }
      },
      "config": {
        "enabled": true,
        "neighbor-address": "10.10.10.5",
        "peer-as": 65003,
        "route-flap-damping": false,
        "send-community": "NONE"
      },
      "ebgp-multihop": {
        "config": {
          "enabled": false,
          "multihop-ttl": 0
        },
        "state": {
          "enabled": false,
          "multihop-ttl": 0
        }
      },
      "error-handling": {
        "config": {
          "treat-as-withdraw": false
        },
        "state": {
          "treat-as-withdraw": false
        }
      },
      "neighbor-address": "10.10.10.5",
      "route-reflector": {
        "config": {},
        "state": {}
      },
      "state": {
        "dynamically-configured": false,
        "enabled": true,
        "established-transitions": "1",
        "last-established": "159096348429",
        "neighbor-address": "10.10.10.5",
        "peer-as": 65003,
        "route-flap-damping": false,
        "send-community": "NONE",
        "session-state": "ESTABLISHED"
      },
      "timers": {
        "config": {
          "connect-retry": "30.0",
          "minimum-advertisement-interval": "30.0"
        },
        "state": {
          "connect-retry": "30.0",
          "minimum-advertisement-interval": "30.0"
        }
      },
      "transport": {
        "config": {
          "mtu-discovery": true
        },
        "state": {
          "mtu-discovery": true,
          "remote-address": "10.10.10.5",
          "remote-port": 0
        }
      },
      "use-multiple-paths": {
        "config": {
          "enabled": false
        },
        "ebgp": {
          "config": {
            "allow-multiple-as": false
          },
          "state": {
            "allow-multiple-as": false
          }
        },
        "state": {
          "enabled": false
        }
      }
    }
  ]
}
```
</p>
</details>

#### Set RPC

The Set RPC is used to modify states.   

The SetRequest message uses the following fields: 
- "delete" field: A set of paths which are to be removed from the data tree. 
- "replace" field: A set of "Update messages" indicating elements of the data tree whose content is to be replaced.
- "update" field: A set of "Update messages" indicating elements of the data tree whose content is to be updated.

An "Update message" is utilised to indicate changes to paths where a new value is required. The Update message contains two fields: path and value.  

For both "replace" and "update" operations, if the path specified does not exist, the target MUST create the data tree element and populate it with the data in the Update message, provided the path and data are valid   

##### update an existing element 

"enabled" is a leaf defined in the openconfig-interfaces.yang file. It is a boolean. Its default value is true. It is the configured state of the interface. This leaf always exists.   
  
```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/interfaces/interface[name=Ethernet4]/config/enabled' 'false'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config interfaces ethernet 4
interface Ethernet4
   shutdown
switch2#
```
</p>
</details>

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/interfaces/interface[name=Ethernet4]/config/enabled' 'true'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config interfaces ethernet 4
interface Ethernet4
switch2#
```
</p>
</details>

##### Create a new element or update/replace it if it already exists

New elements could be new BGP neighbors, new BGP groups ....  

###### Create the BGP element or replace the existing one 

If the element doesnt exist, it will be created.    
If it already exist then it will be replaced.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista replace '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp' '{"global": {"config": {"as": 65002}}}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config sec bgp
router bgp 65002
switch2#
```
</p>
</details>

###### Create a new BGP group element or update the existing one

If the element doesnt exist, it will be created.    
If it already exist then it will be updated.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/peer-groups/peer-group[peer-group-name=XYZ]' '{"config": {"peer-group-name":"XYZ"}}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config sec bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
switch2#
```
</p>
</details>

###### Create a new BGP neighbor element or update the existing one

If the element doesnt exist, it will be created.    
If it already exist then it will be updated.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]' '{"config": {"neighbor-address":"10.10.100.43", "peer-as": 123, "enabled": true, "send-community": "EXTENDED"}}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config sec bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.100.43 remote-as 123
   neighbor 10.10.100.43 send-community extended
   neighbor 10.10.100.43 maximum-routes 12000
switch2#
```
</p>
</details>

###### Create a new BGP neighbor element or replace the existing one

If the element doesnt exist, it will be created.    
If it already exist then it will be replaced.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista replace '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]' '{"config": {"neighbor-address":"10.10.100.43", "peer-as": 123}, "neighbor-address": "10.10.100.43"}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2#show running-config sec bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.100.43 remote-as 123
   neighbor 10.10.100.43 maximum-routes 12000
switch2#
```
</p>
</details>


##### update existing elements

if the BGP neighbor element already exists it will be updated. 
If the BGP neighbor element doesnt exist, the request will fail and it wont be created despite the field "path" is valid (because some of the required data are missing in the field "value" of the "update message").  

These 2 commands are equivalent: 
```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]/config/peer-as' '110'
```
```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]' '{"config": {"peer-as": 110}}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.100.43 remote-as 110
   neighbor 10.10.100.43 maximum-routes 12000
switch2(config)#
```
</p>
</details>


```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]/config/peer-group' 'XYZ'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.100.43 peer group XYZ
   neighbor 10.10.100.43 remote-as 110
switch2(config)#
```
</p>
</details>


```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]' '{"config": {"peer-group": "XYZ","peer-as": 143}}'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  

```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.100.43 peer group XYZ
   neighbor 10.10.100.43 remote-as 143
switch2(config)#
```
</p>
</details>

##### delete an existing element  

The delete field in the SetRequest messaage has a set of paths which are to be removed from the data tree.  

This command will send a SetRequest message with 1 path in the delete field.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista delete '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
switch2(config)#
```
</p>
</details>


##### Modify several states 

We can use all the fields (delete, replace and update) in the SetRequest message, with several paths in each the field.  

This command will send a `SetRequest` message with 3 paths in the field upate and 1 path in the field delete.   

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp' '{"global": {"config": {"as": 65002}}}' update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/peer-groups/peer-group[peer-group-name=XYZ]' '{"peer-group-name":"XYZ", "config": {"peer-group-name":"XYZ"}}' update '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.1]' '{"neighbor-address":"10.10.10.1", "config": {"neighbor-address":"10.10.10.1", "peer-as": 123, "enabled": true, "send-community": "EXTENDED"}}' delete '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.100.43]'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.10.1 remote-as 123
   neighbor 10.10.10.1 send-community extended
   neighbor 10.10.10.1 maximum-routes 12000
switch2(config)#
```
</p>
</details>


This command will send a SetRequest message with 2 paths in the delete field.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista delete '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/neighbors/neighbor[neighbor-address=10.10.10.1]/config/send-community' delete '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp/peer-groups/peer-group[peer-group-name=XYZ]'
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor 10.10.10.1 remote-as 123
   neighbor 10.10.10.1 maximum-routes 12000
switch2(config)#
```
</p>
</details>

##### Use a file to create new elements or replace existing ones 

We can use the file [bgp.json](bgp.json) to create new elements or replace the existing ones 

Several elements will be modified.  For each element: 
- If the element doesnt exist, it will be created.    
- If it already exist then it will be replaced.  

```
./gnmi -addr 10.83.28.203:6030 -username arista -password arista replace '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp' /Users/ksator/Projects/gnmi/bgp.json
```
<details><summary>click me to see the new EOS configuration</summary>
<p>
  
```
switch2(config)#show  running-config section bgp
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ remote-as 65002
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.10.1 remote-as 123
   neighbor 10.10.10.1 maximum-routes 12000
   neighbor 10.10.10.154 peer group XYZ
   neighbor 10.10.10.157 peer group XYZ
switch2(config)#
```
</p>
</details>

#### OpenConfig paths and EOS native paths 

The `origin` field can be used in a `SetRequest`, `GetRequest`, `SubscribeRequest` messages.  
This field is optionnal. It is a string. Its default value is `openconfig`  
The path specified within the message is uniquely identified by the tuple of <origin, path>.  
This field is used to disambiguate the path.  
The origin is used to indicate which organization defined the path.  

In addition to accepting OpenConfig paths in gNMI get/subscribe requests, Arista EOS devices accept also EOS native paths (e.g. Sysdb/Smash/kernel paths) when gNMI requests contains an origin of `eos_native`. 

Examples: 
```
./gnmi -addr 10.83.28.125:6030 -username arista -password arista subscribe origin=openconfig '/network-instances/network-instance/protocols/protocol/bgp/neighbors/neighbor/state'
```
```
./gnmi -addr 10.83.28.125:6030 -username arista -password arista subscribe origin=eos_native '/Sysdb/routing/bgp/export/'
```
```
./gnmi -addr 10.83.28.125:6030 -username arista -password arista subscribe origin=eos_native '/Smash/routing/bgp'       
```
```
./gnmi -addr 10.83.28.125:6030 -username arista -password arista subscribe origin=eos_native '/Kernel/proc/cpu/'
```

#### device configuration end to end demo 

We will use YANG files and pyangbind to generate an OpenConfig configuration file. 
We will then use GNMI (SET gRPC) to configure the device with that OpenConfig configuration file. 

Instructions: 
- Install pyang
  - `pip install pyang`
- Install pyangbind 
  - `pip install pyangbind`
- Download yang files 
  - `git clone https://github.com/openconfig/public.git`
- copy all the yang files from Openconfig into one directory (see above repository for detailled instructions)
- move to that directory and run this command to convert the YANG module openconfig-bgp.yang into the Python module oc_bgp.py
  - `pyang --plugindir $VIRTUAL_ENV/lib/python3.7/site-packages/pyangbind/plugin -f pybind -o oc_bgp.py openconfig-bgp.yang`
- From the directory that has the python module oc_bgp.py, execute the following python code in order to generate an OC configuration

```
from oc_bgp import openconfig_bgp
import pyangbind.lib.pybindJSON as pybindJSON

oc=openconfig_bgp()

oc.bgp.global_.config.as_="65002"

oc.bgp.peer_groups.peer_group.add("XYZ")
oc.bgp.peer_groups.peer_group["XYZ"].config.peer_group_name="XYZ"
oc.bgp.peer_groups.peer_group["XYZ"].config.peer_as=65002

oc.bgp.neighbors.neighbor.add("10.10.10.154")
oc.bgp.neighbors.neighbor["10.10.10.154"].config.neighbor_address="10.10.10.154"
oc.bgp.neighbors.neighbor["10.10.10.154"].config.peer_group="XYZ"
oc.bgp.neighbors.neighbor["10.10.10.154"].config.enabled=True

oc.bgp.neighbors.neighbor.add("10.10.10.157")
oc.bgp.neighbors.neighbor["10.10.10.157"].config.neighbor_address="10.10.10.157"
oc.bgp.neighbors.neighbor["10.10.10.157"].config.peer_group="XYZ"
oc.bgp.neighbors.neighbor["10.10.10.157"].config.enabled=True
```
```
print(pybindJSON.dumps(oc.bgp, mode="ietf"))
{
    "openconfig-bgp:global": {
        "config": {
            "as": 65002
        }
    },
    "openconfig-bgp:neighbors": {
        "neighbor": [
            {
                "neighbor-address": "10.10.10.154",
                "config": {
                    "peer-group": "XYZ",
                    "neighbor-address": "10.10.10.154"
                }
            },
            {
                "neighbor-address": "10.10.10.157",
                "config": {
                    "peer-group": "XYZ",
                    "neighbor-address": "10.10.10.157"
                }
            }
        ]
    },
    "openconfig-bgp:peer-groups": {
        "peer-group": [
            {
                "peer-group-name": "XYZ",
                "config": {
                    "peer-group-name": "XYZ",
                    "peer-as": 65002
                }
            }
        ]
    }
}
```
Verify the current BGP configuration on a device
```
SPINE1(config)#sh running-config section bgp
SPINE1(config)#
```
Use the gRPC SET to load the generated OC configuration file to that device 
```
`./gnmi -addr device_IP:gnmi_port -username username -password password replace '/network-instances/network-instance[name=default]/protocols/protocol[name=BGP]/bgp' /Users/ksator/Projects/gnmi/generated_file.json`
```
Verify the new BGP configuration on that device
```
SPINE1#sh running-config section bgp 
router bgp 65002
   neighbor XYZ peer group
   neighbor XYZ remote-as 65002
   neighbor XYZ maximum-routes 12000
   neighbor 10.10.10.154 peer group XYZ
   neighbor 10.10.10.157 peer group XYZ
SPINE1#
```
et voila :-) 

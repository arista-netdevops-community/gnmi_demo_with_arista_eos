# About this repository 

We will use pyang, pyangbind, and gNMI.  

We will interact with Arista EOS devices using gNMI.   
We will use a gnmi command-line client and the following RPC: capabilites, get, subscribe, update, replace, and delete. 

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

Example: let's validate the modules openconfig-bgp.yang and openconfig-interfaces.yang from openconfig github repository


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

## convert YANG modules into equivalent YIN module

A YANG module can be translated into an XML syntax called YIN. The translated module is called a YIN module. The YANG and YIN formats contain equivalent information using different notations: YIN is YANG in XML. A YANG module can be translated into YIN syntax without losing any information.  

Example (openconfig-bgp.yin is the YIN equivalent of openconfig-bgp.yang)
```
pyang openconfig-bgp.yang -f yin -o openconfig-bgp.yin
ls *.yin
```

## Generate a tree representation of YANG modules for quick visualization

```
yang_modules pyang -f tree openconfig-interfaces.yang                                        
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
yang_modules pyang openconfig-interfaces.yang -f tree --tree-path=/interfaces/interface/state       
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
yang_modules pyang openconfig-interfaces.yang -f tree  --tree-depth=4  
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

## pyang installation

```
python -V              
Python 3.7.7
```
pyang installation from pypi 
```
$ pip install pyangbind
```
```
pip freeze | grep pyang
pyang==2.2.1
pyangbind==0.8.1
```

## converts YANG module into a Python module

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
# gNMI (gRPC Network Management Interface)

We will use [this gnmi command-line client](https://github.com/aristanetworks/goarista/tree/master/cmd/gnmi) and the following RPC: capabilites, get, subscribe, update, replace, and delete.

## requirements on Arista devices 

```
username arista secret 0 arista
ip access-list GNMI
   10 permit tcp any any eq gnmi
management api gnmi
   transport grpc def
     ip access-group GNMI
```

## install the gnmi command-line client 

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

### use the following RPC: capabilites, get, subscribe, update, replace, and delete.





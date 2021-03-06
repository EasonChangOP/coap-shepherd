# coap-shepherd
Network server and manager for lightweight M2M (LWM2M).

[![NPM](https://nodei.co/npm/coap-shepherd.png?downloads=true)](https://nodei.co/npm/coap-shepherd/)  

[![Build Status](https://travis-ci.org/PeterEB/coap-shepherd.svg?branch=develop)](https://travis-ci.org/PeterEB/coap-shepherd)
[![npm](https://img.shields.io/npm/v/coap-shepherd.svg?maxAge=2592000)](https://www.npmjs.com/package/coap-shepherd)
[![npm](https://img.shields.io/npm/l/coap-shepherd.svg?maxAge=2592000)](https://www.npmjs.com/package/coap-shepherd)

## Table of Contents

1. [Overview](#Overview)  
2. [Features](#Features)  
3. [Installation](#Installation)  
4. [Usage](#Usage)  
5. [APIs and Events](#APIs)  

<a name="Overview"></a>
## 1. Overview

[**OMA Lightweight M2M**](http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0) (LWM2M) is a resource constrained device management protocol relies on [**CoAP**](https://tools.ietf.org/html/rfc7252). And **CoAP** is an application layer protocol that allows devices to communicate with each other RESTfully over the Internet.  
 
**coap-shepherd** and **coap-node** modules aim to provide a simple way to build and manage a **LWM2M** network.
![coap-shepherd net](https://github.com/PeterEB/coap-shepherd/blob/master/doc/lwm2m_net.png)  
### LWM2M Server: coap-shepherd

* It is a **LWM2M** Server application framework running on node.js.  
* It follows most parts of **LWM2M** specification to meet the requirements of a machine network and devices management.  
* Supports functionalities, such as permission of device joining, reading resources, writing resources, observing resources, and executing a procedure on a remote device.  
* It follows [**IPSO**](http://www.ipso-alliance.org/smart-object-guidelines/) data model to let you allocate and query resources on remote devices with semantic URIs in a comprehensive manner. Here is an example, all these requests is to read a value from the same resource but with different addressing style.  

```js
// numeric style
cnode.readReq('/3303/0/5700', function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 21 }
});

// semantic style
cnode.readReq('/temperature/0/sensedValue', function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 21 }
});

// hybrid style
cnode.readReq('/temperature/0/5700', function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 21 }
});
```

**Note**: 
* You can find all pre-defined IPSO/OMA-LWM2M identifiers from [lwm2m-id](https://github.com/simenkid/lwm2m-id#5-table-of-identifiers) module. You are also welcome to use your own private identifiers in **coap-shepherd**.  
* Here is the client-side module [**coap-node**](https://github.com/PeterEB/coap-node) that can help you with implementing a LWM2M client node. *coap-node* uses **IPSO** data model to well organize and define resources on a machine node.  

###Acronyms and Abbreviations

* **Server**: LWM2M Server (server running with [coap-shepherd](https://github.com/PeterEB/coap-shepherd))  
* **Client** or **Client Device**: LWM2M Client (machine running with [coap-node](https://github.com/PeterEB/coap-node))  
* **cserver**: instance of CoapShepherd Class  
* **cnode**: instance of CoapNode Class  
* **oid**: identifier of an _Object_  
* **iid**: identifier of an _Object Instance_  
* **rid**: identifier of a _Resource_  
  

<a name="Features"></a>
## 2. Features

* Constrained Application Protocol (CoAP)  
* Based on [node-coap](https://github.com/mcollina/node-coap), a node.js CoAP client/server library  
* CoAP network and devices management  
* Hierarchical data model in Smart-Object-style (IPSO)  
* Client/server interaction through LWM2M-defined interfaces  

<a name="Installation"></a>
## 3. Installation

> $ npm install coap-shepherd --save

<a name="Usage"></a>
## 4. Usage

This example shows how to start a server and allow devices to join the network within 300 seconds after the server is ready:

```js
var cserver = require('coap-shepherd');

cserver.on('ready', function () {
    console.log('Server is ready.');
    cserver.permitJoin(300);    // allow devices to join the network within 300 secs
});

cserver.start(function (err) {  // start the server
    if (err)
        console.log(err);
});
```

<a name="APIs"></a>
## 5. APIs and Events

#### 1. CoapShepherd APIs

In this document, **cserver** denotes an instance of CoapShepherd class. **cserver** is also a singleton exported by `require('coap-shepherd')`.  

* [cserver.start()](#API_start)
* [cserver.stop()](#API_stop)
* [cserver.reset()](#API_reset)
* [cserver.permitJoin()](#API_permitJoin)
* [cserver.list()](#API_list)
* [cserver.find()](#API_find)
* [cserver.remove()](#API_remove)
* [cserver.announce()](#API_announce)
* Events
    * [ready](#EVT_ready), [permitJoining](#EVT_permitJoining)
    * [ind](#EVT_ind)
    * [error](#EVT_error)

#### 2. CoapNode APIs

CoapNode is the class to create software endpoints of the remote Client Devices at server-side. In this document, **cnode** denotes an instance of CoapNode class. You can invoke methods on a `cnode` to operate the remote device.  

* [cnode.readReq()](#API_readReq)
* [cnode.discoverReq()](#API_discoverReq)
* [cnode.writeReq()](#API_writeReq)
* [cnode.writeAttrsReq()](#API_writeAttrsReq)
* [cnode.executeReq()](#API_executeReq)
* [cnode.observeReq()](#API_observeReq)
* [cnode.cancelObserveReq()](#API_cancelObserveReq)
* [cnode.pingReq()](#API_pingReq)
* [cnode.dump()](#API_dump)

*************************************************
## CoapShepherd Class
Server configuration is read from file `config.js` in the `lib` folder of this module.  

<a name="API_start"></a>
### cserver.start([callback])
Start the cserver.  

**Arguments:**  

1. `callback` (_Function_): `function (err) { }`. Get called after starting procedure is done.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.start(function (err) {
    console.log('server is started.');
});
```

*************************************************
<a name="API_stop"></a>
### cserver.stop([callback])
Stop the cserver.

**Arguments:**  

1. `callback` (_Function_): `function (err) { }`. Get called after stopping procedure is done.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.stop(function (err) {
    console.log('server stopped.');
});
```

*************************************************
<a name="API_reset"></a>
### cserver.reset([callback])
Reset the cserver.

**Arguments:**  

1. `callback` (_Function_): `function (err) { }`. Get called after the server restarted.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.reset(function (err) {
    if (!err) 
        console.log('server restarted.');
});
```

*************************************************
<a name="API_permitJoin"></a>
### cserver.permitJoin(time)
Allow or disallow devices to join the network.  

**Arguments:**  

1. `time` (_Number_): Time in seconds for csever to allow devices to join the network. Set `time` to `0` can immediately close the admission.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.permitJoin(300); 
```

*************************************************
<a name="API_list"></a>
### cserver.list()
List records of the registered Client Devices.

**Arguments:**  

1. none

**Returns:**  

* (_Array_): Information of Client Devices. 

**Examples:** 

```js
console.log(cserver.list());

/*
[
    { 
        clientName: 'node1',
        clientId: 1,
        lifetime: 86400,
        version: '1.0.0',
        ip: '192.168.1.112',
        mac: '00:0c:29:3c:fc:7d',
        port: 56643,
        objList: {
            '1': ['0'],
            '3303': ['0']
        }
    }, { 
        clientName: 'node2',
        clientId: 2,
        lifetime: 86400,
        version: '1.0.0',
        ip: '192.168.1.111',
        mac: '30:1a:9f:5d:af:c8',
        port: 56643,
        objList: {
            '1': ['0'],
            '3303': ['0']
        }
    }
] 
*/
```

*************************************************
<a name="API_find"></a>
### cserver.find(clientName)
Find a registered Client Device (cnode) on cserver.  

**Arguments:**  

1. `clientName` (_String_): Name of the Client Device to find for.  

**Returns:**  

* (Object): cnode. Returns `undefined` if not found.  

**Examples:** 

```js
var cnode = cserver.find('foo_name');

if (cnode) {
    // do something upon the cnode, like cnode.readReq()
}
```

*************************************************
<a name="API_remove"></a>
### cserver.remove(clientName[, callback])
Deregister and remove a cnode from cserver.  

**Arguments:**  

1. `clientName` (_String_): Name of the Client Device to be removed.  
2. `callback` (_Function_): `function (err) { }`. Get called after the removal is done.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.remove('foo_name');
```
*************************************************
<a name="API_announce"></a>
### cserver.announce(msg[, callback])
The cserver can use this method to announce(/broadcast) any message to all Client Devices.  

**Arguments:**  

1. `msg` (_String_): The message to announce.  
2. `callback` (_Function_): `function (err) { }`. Get called after message announced.  

**Returns:**  

* (none)

**Examples:** 

```js
cserver.announce('Hum!');
```

*************************************************
<a name="EVT_ready"></a>
### Event: 'ready'
`function () { }`  
Fired when cserver is ready.

*************************************************
<a name="EVT_error"></a>
### Event: 'error'
`function (err) { }`  
Fired when there is an error occurs.

*************************************************
<a name="EVT_permitJoining"></a>
### Event: 'permitJoining'
`function (joinTimeLeft) { }`  
Fired when cserver is allowing for devices to join the network, where `joinTimeLeft` is number of seconds left to allow devices to join the network. This event will be triggered at each tick of countdown.

*************************************************
<a name="EVT_ind"></a>
### Event: 'ind'
`function (msg) { }`  
Fired when there is an incoming indication message. There are 5 types of indication including `'devIncoming'`, `'devLeaving'`, `'devUpdate'`, `'devStatus'`, and `'devNotify'`.  

* **devIncoming**  
     Fired when a Client Device registers to cserver.  

    * msg.type: `'devIncoming'`
    * msg.cnode: `cnode`, the cnode instance of which cnode is incoming
    * msg.data: `undefined`  
    * message examples  

        ```js
        {
            type: 'devIncoming',
            cnode: cnode instance
        }
        ```

* **devLeaving**  
     Fired when a Client Device deregisters from cserver.  

    * msg.type: `'devLeaving'`
    * msg.cnode: `'foo_name'`, the clientName of which cnode is leaving
    * msg.data: `30:1a:9f:5d:af:c8`, the mac address of which cnode is leaving.  
    * message examples  

        ```js
        {
            type: 'devLeaving',
            cnode: 'foo_name',
            data: '30:1a:9f:5d:af:c8'
        }
        ```
* **devUpdate**  
     Fired when a Client Device updates its device attributes.

    * msg.type: `'update'`
    * msg.cnode: `cnode`
    * msg.data: this object at least has a `device` field to denote the name of a Client Device, and it may have fields of `lifetime`, `objList`, `ip`, and `port`.  
    * message examples  

        ```js
        {
            type: 'devUpdate',
            cnode: cnode instance,
            data: {
                lifetime: 42000
            }
        }
        ```

* **devStatus**  
     Fired when there is a cnode going online, going offline, or going to sleep.

    * msg.type: `'devStatus'`
    * msg.cnode: `cnode`
    * msg.data: `'online'`, `'offline'`, or `'sleep'`
    * message examples  

        ```js
        {
            type: 'devStatus',
            cnode: cnode instance,
            data: 'offline'
        }
        ```

* **devNotify**  
     Fired upon receiving an notification of Object Instance or Resource from a Client Device.  

    * msg.type: `'devNotify'`
    * msg.cnode: `cnode`
    * msg.data: notification from a Client Device. This object has fields of `device`, `path`, and `value`.  
    * message examples  

        ```js
        // A Resource notification
        {
            type: 'devNotify',
            cnode: cnode instance,
            data: {
                path: '/temperature/0/sensorValue',
                value: 21
            }
        }

        // An Object Instance notification
        {
            type: 'devNotify',
            cnode: cnode instance,
            data: {
                path: '/temperature/0',
                value: {
                    sensorValue: 21
                }  
            }
        }
        ```

*************************************************

## CoapNode Class

This class provides you with methods to perform remote operations upon a registered Client Device. An instance of this class is denoted as `cnode` in this document. You can use `cserver.find()` with a clientName to find the registered cnode on cserver.  

<a name="API_readReq"></a>
### cnode.readReq(path[, callback])
Remotely read an Object, an Object Instance, or a Resource from the Client Device.

**Arguments:**  

1. `path` (_String_): path of the allocated Object, Object Instance or Resource on the remote Client Device.
2. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status | Status      | Description                               |
        |------------|-------------|-------------------------------------------|
        | '2.05'     | Content     | Read operation is completed successfully. |
        | '4.04'     | Not Found   | Target is not found on the Client.        |
        | '4.05'     | Not Allowed | Target is not allowed for Read operation. |
        | '4.08'     | Timeout     | No response from the Client in 60 secs.   |

    * `rsp.data` (_Depends_): `data` can be the value of an Object, an Object Instance, or a Resource. Note that when an unreadable Resource is read, the status code will be '4.05' and the returned value will be a string '\_unreadable\_'.

**Returns:**  

* (none)

**Examples:** 

```js
// read a Resource
cnode.readReq('/temperature/0/sensedValue', function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 21 }
});

// read an Object Instance
cnode.readReq('/temperature/0', function (err, rsp) {
    console.log(rsp);

    // {
    //    status: '2.05',
    //    data: { 
    //      sensedValue: 21 
    //    } 
    // }
});

// read an Object
cnode.readReq('/temperature', function (err, rsp) {
    console.log(rsp);

    // {
    //    status: '2.05',
    //    data: { 
    //      0: { 
    //            sensedValue: 21 
    //         } 
    //    }
    // }
});

// target not found
cnode.readReq('/temperature/0/foo', function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});

// target is unreadable
cnode.readReq('/temperature/0/bar', function (err, rsp) {
    console.log(rsp);   // { status: '4.05', data: '_unreadable_' }
});
```
*************************************************
<a name="API_discoverReq"></a>
### cnode.discoverReq(path[, callback])
Discover report settings of an Object, an Object Instance, or a Resource on the Client Device.  

**Arguments:**  

1. `path` (_String_): path of the allocated Object, Object Instance, or Resource on the remote Client Device.  
2. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status | Status      | Description                                   |
        |------------|-------------|-----------------------------------------------|
        | '2.05'     | Content     | Discover operation is completed successfully. |
        | '4.04'     | Not Found   | Target is not found on the Client.            |
        | '4.08'     | Timeout     | No response from the Client in 60 secs.       |

    * `rsp.data` (_Object_): This is an object of the report settings. `data.attrs` contains parameters of the setting. If the discovered target is an Object, there will be an additional field `data.resrcList` to list all its Resource identifiers under each Object Instance.  

**Returns:**  

* (none)

**Examples:** 

```js
// discover a Resource
cnode.discoverReq('/temperature/0/sensedValue', function (err, rsp) {
    console.log(rsp);

    // {
    //    status: '2.05',
    //    data: {
    //      attrs: { 
    //        pmin: 10, 
    //        pmax: 90,
    //        gt: 0
    //      }
    //    }
    // }
});

// discover an Object
cnode.discoverReq('/temperature', function (err, rsp) {
    console.log(rsp);

    // {
    //    status: '2.05',
    //    data: {
    //      attrs: { 
    //        pmin: 1, 
    //        pmax: 60
    //      },
    //      resrcList: {
    //          0: [ 'sensorValue', 'units' ]
    //      }
    //    }
    // }

// target not found
cnode.discoverReq('/temperature/0/foo', function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});
```
*************************************************
<a name="API_writeReq"></a>
### cnode.writeReq(path, data[, callback])
Remotely write a data to an Object Instance or a Resource on the Client Device.

**Arguments:**  

1. `path` (_String_): path of the allocated Object Instance or Resource on the remote Client Device.  
2. `data` (_Depends_): data to write to the Object Instance or the Resource. If target is a Object Instance, then the `data` is an Object Instance containing the Resource values.
3. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status | Status      | Description                                    |
        |------------|-------------|------------------------------------------------|
        | '2.04'     | Changed     | Write operation is completed successfully      |
        | '4.00'     | Bad Request | The format of data to be written is different. |
        | '4.04'     | Not Found   | Target is not found on the Client.             |
        | '4.05'     | Not Allowed | Target is not allowed for Write operation.     |
        | '4.08'     | Timeout     | No response from the Client in 60 secs.        |

**Returns:**  

* (none)

**Examples:** 

```js
// target is a Resource
cnode.writeReq('/temperature/0/sensedValue', 19, function (err, rsp) {
    console.log(rsp);   // { status: '2.04' }
});

// target is a Object Instance
cnode.writeReq('/temperature/0', { sensedValue: 87, units: 'F' }, function (err, rsp) {
    console.log(rsp);   // { status: '2.04' }
});

// target not found
cnode.writeReq('/temperature/0/foo', 19, function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});

// target is unwritable
cnode.writeReq('/temperature/0/bar', 19, function (err, rsp) {
    console.log(rsp);   // { status: '4.05' }
});
```
*************************************************
<a name="API_writeAttrsReq"></a>
### cnode.writeAttrsReq(path, attrs[, callback])
Configure the parameters of the report settings of an Object, an Object Instance, or a Resource.  

**Arguments:**  

1. `path` (_String_): path of the allocated Object, Object Instance, or Resource on the remote Client Device.  
2. `attrs` (_Object_): parameters of the report settings.  

    | Property | Type   | Required | Description |
    |----------|--------|----------|-------------|
    | pmin     | Number | No       | Minimum Period. Minimum time in seconds the Client Device should wait between two notifications. |
    | pmax     | Number | No       | Maximum Period. Maximum time in seconds the Client Device should wait between two notifications. When maximum time expires after the last notification, a new notification should be sent. |
    | gt       | Number | No       | Greater Than. The Client Device should notify the Server each time the Observed Resource value greater than this setting with respect to pmin parameter. Only valid for the Resource typed as a number. |
    | lt       | Number | No       | Less Than. The Client Device should notify the Server each time the Observed Resource value less than this setting with respect to pmin parameter. Only valid for the Resource typed as a number. |
    | stp      | Number | No       | Step. The Client Device should notify the Server when the change of the Resource value, since the last report happened, is greater or equal to this setting. Only valid for the Resource typed as a number. |

3. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status | Status      | Description                                           |
        |------------|-------------|-------------------------------------------------------|
        | '2.04'     | Changed     | Write Attributes operation is completed successfully. |
        | '4.00'     | Bad Request | Parameter of the attribute(s) can't be recognized.    |
        | '4.04'     | Not Found   | Target is not found on the Client.                    |
        | '4.08'     | Timeout     | No response from the Client in 60 secs.               |

**Returns:**  

* (none)

**Examples:** 

```js
cnode.writeAttrsReq('/temperature/0/sensedValue', { pmin: 10, pmax: 90, gt: 0 }, function (err, rsp) {
    console.log(rsp);   // { status: '2.04' }
});

// target not found
cnode.writeAttrsReq('/temperature/0/foo', { lt: 100 }, function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});

// parameter cannot be recognized
cnode.writeAttrsReq('/temperature/0/sensedValue', { foo: 0 }, function (err, rsp) {
    console.log(rsp);   // { status: '4.00' }
});
```
*************************************************
<a name="API_executeReq"></a>
### cnode.executeReq(path[, args][, callback])
Invoke an executable Resource on the Client Device. An executable Resource is like a remote procedure call.  

**Arguments:**  

1. `path` (_String_): path of the allocated Resource on the remote Client Device.  
2. `args` (_Array_): arguments to the procedure.  
3. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status   | Status      | Description                                                |
        |--------------|-------------|------------------------------------------------------------|
        | '2.04'       | Changed     | Execute operation is completed successfully.               |
        | '4.00'       | Bad Request | Client doesn’t understand the argument in the payload.     |
        | '4.04'       | Not Found   | Target is not found on the Client.                         |
        | '4.05'       | Not Allowed | Target is not allowed for Execute operation.               |
        | '4.08'       | Timeout     | No response from the Client in 60 secs.                    |

**Returns:**  

* (none)

**Examples:** 

```js
// assume there in an executable Resource with the signature
// function(t) { ... } to blink an LED t times.
cnode.executeReq('/led/0/blink', [ 10 ] ,function (err, rsp) {
    console.log(rsp);   // { status: '2.04' }
});

// target not found
cnode.executeReq('/temperature/0/foo', function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});

// target is unexecutable
cnode.executeReq('/temperature/0/bar', function (err, rsp) {
    console.log(rsp);   // { status: '4.05' }
});
```
*************************************************
<a name="API_observeReq"></a>
### cnode.observeReq(path[, callback])
Start observing an Object Instance or a Resource on the Client Device. **coap-shepherd** don't support the observation on an Object at this moment.  

**Note**

* Server always re-initiate observation requests for the previous observations whenever client registers. 
* When a client deregisters, server will assume past states are nullified including the previous observations.

**Arguments:**  

1. `path` (_String_): path of the allocated Object Instance or Resource on the remote Client Device.
2. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status   | Status      | Description                                  |
        |--------------|-------------|----------------------------------------------|
        | '2.05'       | Content     | Observe operation is completed successfully. |
        | '4.04'       | Not Found   | The target is not found on the Client.       |
        | '4.05'       | Not Allowed | Target is not allowed for Observe operation. |
        | '4.08'       | Timeout     | No response from the Client in 60 secs.      |

   * `rsp.data` (_Depends_): `data` can be the value of an Object Instance or a Resource. Note that when an unreadable Resource is observe, the returned value will be a string '\_unreadble\_'.

**Returns:**  

* (none)

**Examples:** 

```js
cnode.observeReq('/temperature/0/sensedValue', function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 27 }
});

// target not found
cnode.observeReq('/temperature/0/foo', function (err, rsp) {
    console.log(rsp);   // { status: '4.04' }
});

// target is not allowed for observation
cnode.observeReq('/temperature/0/bar', function (err, rsp) {
    console.log(rsp);   // { status: '4.05' }
});
```
*************************************************
<a name="API_cancelObserveReq"></a>
### cnode.cancelObserveReq(path[, callback])
Stop observing an Object Instance or a Resource on the Client Device. **coap-shepherd** don't support the observation on an Object at this moment.  

**Arguments:**  

1. `path` (_String_): path of the allocated Object Instance or Resource on the remote Client Device.  
2. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)  

        | rsp.status   | Status      | Description                                         |
        |--------------|-------------|-----------------------------------------------------|
        | '2.05'       | Content     | Observation is successfully cancelled.              |
        | '4.04'       | Not Found   | The target is not found on the Client.              |
        | '4.05'       | Not Allowed | The target has not yet been observed on the Client. |
        | '4.08'       | Timeout     | No response from the Client in 60 secs.             |

**Returns:**  

* (none)

**Examples:** 

```js
cnode.cancelObserveReq('/temperature/0/sensedValue', function (err, rsp) {
    console.log(rsp);   // { status: '2.05' }
});

// target has not yet been observed
cnode.cancelObserveReq('/temperature/0/foo', function (err, rsp) {
    console.log(rsp);   // { status: '4.05' }
});
```
*************************************************
<a name="API_pingReq"></a>
### cnode.pingReq([callback])
Ping the Client Device.  

**Arguments:**  

1. `callback` (_Function_): `function (err, rsp) { }`. The `rsp` object has a status code to indicate whether the operation is successful.  

    * `rsp.status` (_String_)

        | rsp.status | Status      | Description                               |
        |------------|-------------|-------------------------------------------|
        | '2.05'     | Content     | Ping operation is successful.             |
        | '4.08'     | Timeout     | No response from the Client in 60 secs.   |

    * `rsp.data` (_Number_): The approximate round trip time in milliseconds.

**Returns:**  

* (none)

**Examples:** 

```js
cnode.pingReq(function (err, rsp) {
    console.log(rsp);   // { status: '2.05', data: 10 }
});
```
*************************************************
<a name="API_dump"></a>
### cnode.dump()
Dump record of the Client Device.  

**Arguments:**  

1. none

**Returns:**  

* (_Object_): A data object of cnode record.

    |   Property   |  Type   |  Description                                                                |
    |--------------|---------|-----------------------------------------------------------------------------|
    |  clientName  | String  | Client name of the device.                                                  |
    |  ip          | String  | Ip address of the device.                                                   |
    |  mac         | String  | Mac adderss of the device.                                                  |
    |  port        | Number  | Port of the device.                                                         |
    |  lifetime    | Number  | Lifetime of the device.                                                     |
    |  version     | String  | LWM2M version.                                                              |
    |  objList     | Object  | The list of Objects supported and Object Instances available on the device. |
    |  so          | Object  | All of the Objects, Object Instances and Resources.                         |

**Examples:** 

```js
console.log(cnode.dump());

/*
{
    'clientName': 'foo_Name',
    'clientId': 1,
    'lifetime': 86400,
    'version': '1.0.0',
    'ip': '127.0.0.1',
    'mac': '00:0c:29:3c:fc:7d',
    'port': 56643,
    'objList': {
        '1':['0'],
        '3303':['0']
    },
    'so': {
        'lwm2mServer': {
            '0': { 
                'lifetimev:86400,
                'defaultMinPeriod':1,
                'defaultMaxPeriod':60
            }
        },
        'temperature': {
            '0': { 
                'sensorValue':19,
                'units':'C'
            }
        }
    }
}
*/
```

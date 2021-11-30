# How to automate VMs network settings

Here is some tips how to define some network properties of VM

## 1. Using IP to MAC bindings in libvirts ```network```

### 1. Setting up ```interface``` MAC-address

First we should define MAC-address:
```bash
$ virsh edit <vm_name>
```

Then we should create or edit block in ```domain > devices > interface``` definition. We will use 

```test-net``` as a network name:

```xml
...
<devices>
  ...
  <interface type='network'>
    <source network='test-net'/>
    <mac address='<mac-address>'/>
  ...
  </interface>
  ...
</devices>
...
```

> Tip: you can use following command to pregenerate MAC-address and use it elsewhere:
> ```bash
> $ tr -dc A-F0-9 < /dev/urandom | head -c 10 | sed -r 's/(..)/\1:/g;s/:$//;s/^/02:/'
> ```

### 2. Create an IP to MAC binding in ```network``` description

Addresses bind in DHCP definition of ```network``` like this:

```xml
<network>
  <name>test-net</name>
  ...
  <ip address="192.168.122.1" netmask="255.255.255.0" localPtr="yes">
    ...
    <dhcp>
      <range start='192.168.122.100' end='192.168.122.254'/>
      <host mac='52:54:00:6c:3c:01' name='vm1' ip='192.168.122.11'/> <!-- Here is MAC-to-IP binding-->
      <host mac='52:54:00:6c:3c:02' name='vm2' ip='192.168.122.12'/> <!-- Here is MAC-to-IP binding-->
      <host mac='52:54:00:6c:3c:03' name='vm3' ip='192.168.122.12'/> <!-- Here is MAC-to-IP binding-->
    </dhcp>
    ...
  </ip>
  ...
</network>
```
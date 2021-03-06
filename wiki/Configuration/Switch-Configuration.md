## Switch Configuration

Stacki handles switch configuration which includes:

* IP Addressing
* Assign Ports to VLAN
* Status of ports on the switch

A switch to Stacki is just a host with some minor differences:

* Appliance type is `switch`
* Needs `switch_model` attribute set
* Needs `switch_username` attribute set
* Needs `switch_password` attribute set

### Managing the switch

To get up and running we just need to:

* Add the switch
* Give it an interface
* Add some hosts to it

#### Adding a switch

Adding a switch is just like adding a host:

* `stack add switch private-switch rack=1 rank=1 model=x1052 username=admin password=admin`
* `stack load switchfile file=switchfile.csv`

The `switchfile` example spreadsheet looks like this:

```
NAME,RACK,RANK,MODEL,INTERFACE,IP,MAC,NETWORK,USERNAME,PASSWORD
private-switch,10,20,x1052,eth1,192.168.2.1,00:11:22:33:44:55,private,admin,admin
```

If in the future you would like to change the model, username, or password you can use `stack set host attr` and change the attrs:

* switch_model
* switch_username
* switch_password

#### Adding an interface

***If you used the spreadsheet, you can skip this step***

Adding an interface is exactly the same as with hosts. The important part here is that the network you assign to the interface has `pxe=true` if you plan on setting the ip address with dhcp.

`stack add switch interface private-switch interface=eth1 mac=00:11:22:33:44:55 ip=10.0.0.1 network=private`

**Warning** If you do not add an interface name, the DHCP configuration will not include the switch's interface. 

#### Adding hosts

Adding hosts to be managed on the switch can be done two ways:

* `stack add switch host private-switch host=backend-0-0 port=10`
* `stack load switch hostfile file=switch_hostfile.csv`

The `switch_hostfile` example spreadsheet looks like this:

```
NAME,SWITCH,PORT,INTERFACE
backend-0-1,primary-switch,1,eth1
backend-0-2,primary-switch,2,eth1
backend-0-3,primary-switch,3,eth1
backend-0-4,primary-switch,4,eth1
```

#### Setting VLAN

The VLAN ID that is used to configure the switch comes from the host's interface and can be updated using:

* `stack set host interface vlan`
* Updating the host configuration spreadsheet (This is the same csv used in `stack load hostfile`)

#### Apply the switch configuration

`stack sync switch`

This will run `stack report switch` first and then upload the new config to the switches that are configured.

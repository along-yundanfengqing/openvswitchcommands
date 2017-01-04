#Basic Commands

Create a vSwitch called ovsbr1
>ovs-vsctl add-br ovsbr1

Show all configurations
>ovs-vsctl show

Show configuration of ovsbr1
>ovsvsctl show ovsbr1

Add eth0 to ovsbr1
*First remove any IP in eth0
>ifconfig eth0 0 up
Then
>ovs-vsctl add-port ovsbr1 eth0

Assuming the 10.0.0.1/24 IP was on eth0, add the 10.0.0.1 IP to ovsbr1
>ifconfig ovsbr1 10.0.0.1/24 up


#Intermediate Commands

-----VLAN------
Create a fake bridge called "myvlan10" on top of ovsbr1 with native vlan tag 10
>ovs-vsctl add-br myvlan10 ovsbr1 10

Assign an IP to myvlan10
>ifconfig myvlan10 10.0.10.1/24 up

Assign native untagged vlan 10 to eth0 being a trunk port
>ovs-vsctl set port eth0 tag=10 vlan_mode=native-untagged


-----BOND-------
Create a bond port with two ports in active-backup setup, eth0 and eth1 are trunk ports going to different switches for HA setup.
Both switches must have exactly the same trunking VLANs and native vlans going to ovsbr1.
>ovs-vsctl add-bond ovsbr1 bond0 eth0 eth1 bond_mode=active-backup
Monitor the bond0
>ovs-appctl bond/show bond0

Bandwidth aggregation is also possible for active/active setup but both eth0 and eth1 must connect to the same switch, creating an
active/active setup connecting to two different switches using the same bond is not advised, network loops can occur.


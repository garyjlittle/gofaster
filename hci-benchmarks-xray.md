
### Installing and using X-Ray
#### Pre-requisites
##### X-Ray VM location
To use __all__ of the X-Ray functionality we require that X-Ray be run externally to the cluster under test.  The X-Ray VM need not be on an HCI cluster, in fact it is quite normal to run the X-Ray VM on a laptop.  The X-Ray VM will communicate with the cluster manager (vCenter, Prism, SCVMM) and upload the base vm images, to the cluster under test.
##### X-Ray network requirements
X-Ray simulates applications, rather than aggregations of applications, so for instance to simulate 100 VDI users on a host, we provision 100 VDI VMs and each VM does 1 Desktops IO pattern.  For very large VDI installs there will be a requirement for 100's of IP addresses.  The IP addresses can either be provided via DHCP, or using non-routable zeroconf.  If zeroconf is used - the X-Ray VM will need to be on the same layer-2 network as the X-Ray worker VMs, since X-Ray VM and X-Ray workers need to communicate.

### Install videos
This is best shown as a video
* Connecting to the X-Ray VM and adding a target
* Running an X-Ray test, simple IO performance
* Running an X-Ray test scenario demonstrating noisy neighbor behavior
* Running an X-Ray test scenario demonstrating failure 


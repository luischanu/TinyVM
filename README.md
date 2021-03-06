```
 /######## /##                     /##    /## /##      /##
|__  ##__/|__/                    | ##   | ##| ###    /###
   | ##    /## /#######  /##   /##| ##   | ##| ####  /####               Created by
   | ##   | ##| ##__  ##| ##  | ##|  ## / ##/| ## ##/## ##          -----------------------
   | ##   | ##| ##  \ ##| ##  | ## \  ## ##/ | ##  ###| ##               Luis Chanu
   | ##   | ##| ##  | ##| ##  | ##  \  ###/  | ##\  # | ##          VCDX #246 & NSX vExpert
   | ##   | ##| ##  | ##|  #######   \  #/   | ## \/  | ##
   |__/   |__/|__/  |__/ \____  ##    \_/    |__/     |__/
                         /##  | ##                        
                        |  ######/                        
                         \______/                         

                        Version 2.7

```

<br>

# Table of Contents

* [Description](#description)
  * [TinyVM Web Page](#tinyvm-web-page)
* [VM Details](#vm-details)
  * [Virtual Hardware](#virtual-hardware)
  * [VM Options](#vm-options)
  * [Software](#software)
* [Product Features](#product-features)
  * [Static IPv4 Address Configuration](#static-ipv4-address-configuration)
  * [IPv6 Support](#ipv6-support)
  * [HTTP/HTTPS Support](#httphttps-support)
* [TinyVM Download](#tinyvm-download)
* [Version History](#version-history)
* [Known Issues](#known-issues)
* [Credits](#credits)

<br>

# Description
TinyVM was born out of a desire by [Luis Chanu](https://www.linkedin.com/in/luischanu/) to have a portable VM with a small resource footprint that could be used to easily test/verify load balancing and assist in basic network bandwidth testing using [iPerf3](https://iperf.fr/).  Additionally, as a co-developer of [SDDC.Lab](https://github.com/rutgerblom/SDDC.Lab), Luis thought TinyVM could serve to demonstrate the WorkloadVM functionality that he added to that project.

TinyVM is based on [Tiny Core Linux](http://www.tinycorelinux.net/), and obtains its IPv4 address via DHCP, and it's IPv6 address via Auto-Configuration.  It includes a http web server that displays connection oriented information on it's web page that has a border color which is randomly chosen at each VM startup. This random color helps aid the user's identification of the various VMs when load balance testing is being performed.  The web page also displays all of the IPv4 and IPv6 addresses currently used by TinyVM.

As part of the VM power-on process, it also automatically starts an iPerf3 server, which iPerf3 clients can then target to perform bandwidth testing.


## TinyVM Web Page

Here are examples of the web page that TinyVM generates for IPv4 and IPv6 requests:
<br>
<p align="center">
<img src="screenshots/TinyVM-Web-Page-IPv4.png" alt="TinyVM IPv4 Screenshot">
<br><br>
<img src="screenshots/TinyVM-Web-Page-IPv6.png" alt="TinyVM IPv6 Screenshot">
</p>

<br>

# VM Details

TinyVM is built in a vSphere environment, and exported to an [Open Virtualization Format](https://en.wikipedia.org/wiki/Open_Virtualization_Format), and packaged in an [Open Virtual Appliance](https://en.wikipedia.org/wiki/Open_Virtualization_Format), or OVA, package.


## Default User Credentials

TinyVM is built with the following default user credentials:

| Username | Password |
|----------|----------|
| tc       | VMware1! |
| root     | VMware1! |


## Virtual Hardware

TinyVM is provisioned with the following Virtual Hardware configuration:

| Virtual Hardware | Setting/Configuration                    | Notes/Comments |
|------------------|------------------------------------------|-------------------------------------------------------------------|
| CPU              | 1                                        | Enabled "Expose hardware assisted virtualization to the guest OS" |
| Memory           | 192 MB                                   | |
| Hard Disk        | 80 MB                                    | Using IDE(0:0) |
| Network Adapter  | Connect To DHCP Enabled Network Segment  | Adapter Type: VMXNET3 |
| CD/DVD Drive     | Client Device                            | Using IDE(0:1) |
| SCSI Controller  | *** DELETED ***                          | |
| Compatibility    | ESXi 5.5 and later (VM Hardware version 10) | This allows TinyVM to be deployed into just about any environment |


## VM Options

TinyVM is provisioned with the following VM Options:

| VM Option        | Setting/Configuration                    | Notes/Comments |
|------------------|------------------------------------------|----------------|
| Guest OS Family  | Linux                                    | |
| Guest OS Version | Other 3.x or later Linux (32-bit)        | |


## Software

TinyVM was built with the following software:
 * [Tiny Core Linux version 13.0 (Core)](http://www.tinycorelinux.net/)
 * [IPv6 NetFilter](https://www.netfilter.org/) (Provides IPv6 support)
 * [Open-VM-Tools](https://github.com/vmware/open-vm-tools)
 * [OpenSSH](https://www.openssh.com/)
 * [TCPDump](https://www.tcpdump.org/)
 * [NGINX](https://www.nginx.com/)
 * [iPerf3](https://iperf.fr/)
 * [pcre2](https://www.pcre.org/) (Required by PHP-FPM)
 * [pcre](https://www.pcre.org/) (Required by Open-VM-Tools)
 * [curl](https://curl.se/)
 * [wget](https://www.gnu.org/software/wget/)

<br>

# Product Features

## Static IPv4 Address Configuration

By default, TinyVM obtains it's IPv4 address via DHCPv4.  However, you can now assign a static IPv4 address to TinyVM.  Should you assign a static IPv4 address to TinyVM, and later decide you want to revert it back to using DHCPv4, you will need to redeploy it from the OVA file as there is no "undo" to this feature.

To assign an IPv4 address to TinyVM, follow these steps:

1. Deploy TinyVM from the OVA file.
2. Login to TinyVM from either the console or via a SSH session.
3. To assign the static IPv4 address, run the following command:
```
sudo /opt/set-ipv4-address.sh <IPv4Address> <SubnetMask> <BroadcastAddress> <DefaultGateway> <DNSServer> <DNSDomain>

  where:
      <IPv4Address>.......is the static IPv4 address to set the eth0 interface to
      <SubnetMask>........is the subnet mask in dot decimal notation (i.e. 255.255.255.0)
      <BroadcastAddress>..is the IPv4 subnet broadcast address
      <DefaultGateway>....is the IPv4 gateway that should be used for all off-net destinations
      <DNSServer>.........is the IPv4 address of the DNS server to use to resolve names
      <DNSDomain>.........is the default DNS search domain to use

You can also get help on the above command by entering just the command without any arguments.
```
4. Once the static IPv4 address is applied, you will see a message indicating that ```TinyVM WILL RESTART IN 5 SECONDS```, at which point TinyVM will restart.

5. TinyVM is now configured to use a static IPv4 address.  This address will be persistent, and survive across reboots/restarts.  The login banner page will also now indiciate that the IPv4 address is statically configured.

## IPv6 Support
TinyVM is now dual-stacked (IPv4/IPv6), and responds to either IP protocol version.

## HTTP/HTTPS Support
TinyVM's web page can now be reached via both HTTP and HTTPS, via either IPv4/IPv6.  Self-Signed Certificates are automatically generated each time TinyVM starts.  TinyVM is configured to support TLSv1.0, TLSv1.1, TLSv1.2, and TLSv1.3, as well as current Elliptic Curve Diffie-Hellman Ephemeral (ECDHE) ciphers.

## Static IPv6 Address Configuration

By default, TinyVM obtains it's IPv6 address via Auto-Configuration.  However, you can now assign a static IPv6 address to TinyVM.  Should you assign a static IPv6 address to TinyVM, and later decide you want to revert it back to using Auto-Configuration, you will need to redeploy it from the OVA file as there is no "undo" to this feature.  Althought IPv6 does support the assignment of multiple IPv6 addresses to an interface, it currently is not supported by TinyVM.  Only the most recent static IPv6 address assigned will be used.

To assign an IPv6 address to TinyVM, follow these steps:

1. Deploy TinyVM from the OVA file.
2. Login to TinyVM from either the console or via a SSH session.
3. To assign the static IPv6 address, run the following command:
```
sudo /opt/set-ipv6-address.sh <IPv6Address> <PrefixLength> <DefaultGateway> <DNSServer> <DNSDomain>

  where:
      <IPv6Address>     is the static IPv6 address to set the eth0 interface to
      <PrefixLength>    is the IPv6 subnet prefix length without the leading '/' (i.e. 64)
      <DefaultGateway>  is the IPv6 link-local gateway address that should be used for all off-net destinations
      <DNSServer>       is the IPv6 address of the DNS server to use to resolve names
      <DNSDomain>       is the default DNS search domain to use

You can also get help on the above command by entering just the command without any arguments.
```
4. Once the static IPv6 address is applied, you will see a message indicating that ```TinyVM WILL RESTART IN 5 SECONDS```, at which point TinyVM will restart.

5. TinyVM is now configured to use a static IPv4 address.  This address will be persistent, and survive across reboots/restarts.  The login banner page will also now indiciate that the IPv6 address is statically configured.

<br>

# TinyVM Download

TinyVM.ova can be found in the ```images``` directory.

If you do not see an ```images``` directory, then you are in a develpment branch, and the OVA is not yet available.  Please look at a previous branch version for a released OVA.

<br>

# Version History

## Version 1.0
* Internal testing
* Never released to the public

## Version 1.1
* Internal testing
* Never released to the public

## Version 1.2
* Released 10-MAR-2022
* Intial public release of TinyVM

## Version 1.3
* Released 11-MAR-2022
* Added login banner to display IPv4 address

## Version 1.4
* Released 12-MAR-2022
* Added the ability to statically assign IPv4 address to TinyVM
* Modified login banner indicate if the IPv4 address is assigned via DHCP or static

## Version 1.5
* Released 15-MAR-2022
* Had to increase the VM's provisioned resources to support additional features
* Renamed the script to set static IPv4 address from ```/opt/set-ip-address.sh``` to ```/opt/set-ipv4-address.sh```
* IPv6 support added to VM.  See note in [IPv6 Support](#ipv6-support) section above on current limitations
* IPv6 address information, for both Link-local and Global addresses, has been added to the login banner
* Although the web server itself does not support IPv6, the web page has been updated to display the IPv6 address information
* Small notice added to the bottom of the web page indicating that the page was generated by [TinyVM](https://github.com/luischanu/TinyVM)
* Ran into issue where IPv6 addresses were taking a while to register with the OS, so needed to add some delays.  Thus, you may notice a small delay when logging into the VM due to additional delay that was added to ensure IPv6 has time to be noticed by the OS.
* Due to same IPv6 issues, had to introduce delay for startup of the web server.  Thus, after the VM starts, the website will not be available for a minute (or so) while we allow IPv6 to be fully installed and ready for use.

## Version 2.0
* Released 22-MAR-2022
* Replaced Busybox-httpd web server with NGINX
* Enabled full dual-stack (IPv4 and IPv6) support
* Web page converted from a static page to a PHP dynamic web page that is updated with each request
* As part of the new dynamic web page, the border color changed from being set at power-on, to changing with each web request.  This is temporary, and the goal is to change this functionality back in a future version.
* Added various web session specific information to the web page, including:
  * Client Port
  * X-Forwarded-For
  * Server Port
  * Server Protocol
  * HTTP Method
  * Host Header
  * Requested URI
  * Current Time (GMT)

## Version 2.1
* Released 23-MAR-2022
* The border color has been reverted back to how it functioned in previous versions.  The border color is now randomly chosen at VM power on, rather than per web request, and continues to be used until the VM is powered off.

## Version 2.2
* Released 28-MAR-2022
* Reordered web page elements
* Added IP addresses being used by TinyVM to web page
* Added version and IP information to browser tab

## Version 2.3
* Released 29-MAR-2022
* Some internal scripts were cleaned up
* Added HTTPS support for both IPv4 and IPv6
* Server Self-Signed Certificate is automatically recreated each time TinyVM is powered-on or restarted

## Version 2.4
* Released 30-MAR-2022
* Enabled HTTP/2 for HTTPS requests.  HTTP connections will continue to use HTTP/1.1.

## Version 2.5
* Released 5-APR-2022
* Added favicon.ico so that browser tab now shows icon in left, and load balancers no longer create 404 errors in their logs indicating that it couldn't locate ```favicon.ico``` file.  See credits section below for font license and copyright information for font used.
* When clicking on either the TinyVM logo at the top, or the TinyVM link at the bottom, those links now open new browser tabs rather than over-write the TinyVM web page.
* Table margins have been modified so that web page fields format promperly regardless of address family being used.

## Version 2.6
* Released 8-APR-2022
* To support active health checking, added /health.html web page which returns "SUCCESS"
* Added local files of various sizes which can be downloaded by the user to perform basic download speed tests.  Currently, the the following sizes are available: 1KB, 10KB, 100KB, 500KB, 1MB, and 5MB.

## Version 2.7
* Released 21-APR-2022
* Modified login banner to show the dynamic/static configuration status of each IP address family.
* Modified how the login banner displays the various IPv4 and IPv6 addresses in use by TinyVM.
* Added the ability to statically assign an IPv6 Global Unicast address to TinyVM.

<br>

# Known Issues

* When statically assigning an IPv6 address, IPv6 Auto-Configuration is not disabled, and the default gateway is still learned via the IPv6 Router Advertisement from the gateway.  However, the statically configured gateway is still used over the dynamically learned default gateway as it's assigned a lower administrative distance.  This can be observed with the following command: ```route -A inet6```

<br>

# Credits

## Technical Articles/Blogs
While searching for other similar VMs, [Luis Chanu](https://www.linkedin.com/in/luischanu/) came across a couple of articles that inspired him and gave him some ideas, so he thought he would mention them here.  So, a big thank you goes out to the following people for their articles:
* [Maciej Jedrzejczyk](https://cloudarchitectblog.wordpress.com/about/) at [cloudarchitectblog](https://cloudarchitectblog.wordpress.com) for his [How to build your own yVM: step-by-step process](https://cloudarchitectblog.wordpress.com/2015/11/11/how-to-build-your-own-yvm-step-by-step-process/) article.
* [Sheng Chen](https://route179.dev/about/) at [Route179](https://route179.dev/) for his [Create a Tiny Core Linux VM Template for vSphere Lab environment](https://route179.dev/2021/02/21/create-a-tiny-core-linux-vm-template-for-vsphere-lab-environment/) article.

## Font License & Copyright

The [Staatliches](https://github.com/googlefonts/staatliches) font was used to create the web site icon that shows up in the web browser tab.  Although the font is not used anywhere else in the application, in keeping with the conditions and spirit of the SIL Open Font License, you can find the full SIL Open Font License and Copyright notice [HERE](http://scripts.sil.org/OFL).

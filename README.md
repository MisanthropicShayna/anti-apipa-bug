# Anti Automatic Private IP Assignment (Bug)
This program is a cheap patch to a bug that I repeatedly encounter involving Windows APIPA. When Windows fails to automatically acquire an IPv4 address through DHCP, it will attempt to self-assign one in the range of `169.254.0.1 - 169.254.255.254` and will continue attempting to contact the DHCP server until it can acquire an address from it. This process is called automatic private IP address asignment (APIPA), and is an intended feature. However, despite having a manual IPv4 address configured, and APIPA disabled across all adapters, this bug causes Windows to continue self-assigning IP addresses in the background. Since a manual address is already configured, it ends up adding a new address to the same adapter, which prevents this from outright blocking the network connection on said adapter as the manual one still applies and functions. This does however cause complications for certain programs - especially games - that establish peer-to-peer connections, as they often do not choose the correct manually assigned address, and instead send the bogus APIPA address to the recieving client, resulting in time-outs.

This program "fixes" this problem by looping through your adapters and looking for any IP addresses that match a regular expression of an IPv4 address in the APIPA range, and then deletes them. The frequency of these checks can be specified in milliseconds through the `--rate (-r)` commang line argument. It also requires elevated privilages in order to remove said addresses, so ensure admin rights are granted if you run this yourself.

_Footnote: I've noticed that Windows seems to give up self-assigning IP addresses after enough deletions, so the program may not need to run constantly, but this behavior is inconsistent so I recommend keeping it open if you're having APIPA troubles._

## Relevant Links
- [Link-Local-Address Wikipedia](https://en.wikipedia.org/wiki/Link-local_address)
- [IP_ADAPTER_INFO Struct MSDN](https://docs.microsoft.com/en-us/windows/win32/api/iptypes/ns-iptypes-ip_adapter_info)
- [IP_ADDR_STRING Struct MSDN](https://docs.microsoft.com/en-us/windows/win32/api/iptypes/ns-iptypes-ip_addr_string)
- [GetAdapterInfo() MSDN](https://docs.microsoft.com/en-us/windows/win32/api/iphlpapi/nf-iphlpapi-getadaptersinfo)
- [DeleteIPAddress() MSDN](https://docs.microsoft.com/en-us/windows/win32/api/iphlpapi/nf-iphlpapi-deleteipaddress)

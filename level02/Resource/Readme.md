# level02
A surprise awaits me directly in the home folder of the level02 user, where I just landed.
There's a single file called `level02.pcap`, which is 8.2KB

PCAP stands for Packet Capture. It's a file that contains a dump of the packets exchanged by connected devices on a network.
The most well known tool of network trafic analysis is a GUI called Wireshark, it's able to generate pcap files by listening to a network interface, and to read such files as well.

# Resolution

We are going to read this pcap file with the CLI twin of Wireshark called TShark.
After having extracted the file from the machine to our testing environment, we are able to dig into it.

A mere `tshark -r level02.pcap` tells us that it's a dump of a TCP (over IP over Ethernet) communication between two machines.
Let's extract the data from this exchange.

```sh
tshark -2 -r level02.pcap -R "data.data" -T fields -e data.data > data.txt`
```
`-R "data.data"` will restrict the export to only packets which have an actual TCP payload, ie a data field, which we will then be singling out in the export;

data.txt is now a bunch of hex codes, with each new line symbolising a new packet in the exchange.
Let's put this data into an hex to ASCII converter

The data we extracted seems to be a dump of some trafic between an emulated terminal (pts) and its host.

A connection attempt to user wwwbugs happens during this capture.
The Password input line is as such
```
Password: ft_wandrNDRelL0L`
```
The 0x7F special character corresponds to pressing the DEL key.

As such, the input password in this transaction is: `ft_waNDReL0L`

Sure enough, that's all we need to log into flag02 !
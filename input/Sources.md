# Specimen Sources

"Representative" Protocols:

* binary: DNS, NTP, DHCP, SMB, NBNS
    * missing specimens for: NFS, RPC, Modbus, ZeroAccess (Botnet-Protocol used by Netzob)
    * non-Application-Layer: TCP, ICMP, ARP

## Tools
* Filter traces by tshark: 
* Concatenate pcaps: `mergecap -F pcap -w OUTFILE INFILES`
* Change encapulation: `editcap -F pcap -T ENCTYPE INFILE OUTFILE`
* Deduplicate and truncate to fixed size: `prep_deduplicate-trace.py PCAP --p [N]`



## SMIA
[Netresec-Page](http://download.netresec.com/pcap/smia-2011/SMIA_2011-10-10_08%253A03%253A19_CEST_632834000_file1.pcap)  

### NTP
* ntp_SMIA-20111010.pcap
    * from SMIA_2011-10-10_08-03-19_CEST_632834000_file1.pcap
    * filtered by `tshark -2 -R "ntp && !icmp" ... -F pcap`
    * by `python src/prep_deduplicate-trace.py ntp_SMIA-20111010.pcap --p [N]`
    * filter out `ntp.flags.mode == 6`

### DHCP
* dhcp_SMIA-20111010_deduped-100.pcap
    * from SMIA_2011-10-10_08_632834000_file1-splits/dhcp
    * filtered for `bootp`
    * by `python src/prep_deduplicate-trace.py dhcp_SMIA-20111010.pcap --p [N]`
    * **loops > 100h when printing a symbol after netzob inference**
* dhcp_SMIA20111010-one-clean_deduped-100.pcap
    * dhcp-SMIA20111010-one.pcap (which is SMIA_2011-10-10_08_632834000 filtered for bootp)
    * filtered by `!bootp.option.user_class && !icmp`
* dhcp_SMIA20111010-one_deduped-995.pcap
    * dhcp-SMIA20111010-one.pcap (which is SMIA_2011-10-10_08_632834000 filtered for bootp)
    * filtered for `bootp.dhcp`
* dhcp_SMIA2011101X_deduped-10000.pcap
    * from SMIA_2011-10-10_08_632834000_file1-splits/dhcp
      merged with dhcp_SMIA_2011-10-11_07-38-27_CEST_961090000_file1-filtered.pcap
    * filtered by: `!bootp.option.user_class && !icmp` and for `bootp.dhcp`

### Netbios Name Server
* nbns_SMIA20111010-one_deduped-100.pcap
    * from SMIA_2011-10-10_08_632834000_file1-splits/nbns
    * filtered for `nbns && !icmp && !nbns.type == 33`
    * deduplicated and truncated by `python src/prep_deduplicate-trace.py`...

### SMB
* smb_SMIA20111010-one_deduped-100.pcap
    * from SMIA_2011-10-10_08_632834000_file1-splits/smb
    * filtered for `smb && !smb2 && !lanman && !smb.dfs.referral.version && !mailslot && !smb.mincount && !dcerpc && !nbdgm && !nbss.continuation_data && !smb.remaining == 1024`
        * afterwards filter repeatedly by `!(smb.trans2.cmd && _ws.expert.group == "Sequence")`
        * alternatively: `!smb.trans2.cmd || (smb.trans2.cmd > 0 && smb.trans2.cmd < 0xffff)`
    * -- multiple find_first2 files
    * deduplicated and truncated by `python src/prep_deduplicate-trace.py`...
    

## iCTF 2010
[UCSB](http://ictf.cs.ucsb.edu/ictfdata/2010/dumps/ictf2010pcap.tar.gz)  
 
### IRC
* irc_ictf2010-42.pcap
    * from file ictf2010.pcap42
    * filtered by `tshark -2 -R "irc && !(icmp || tcp.analysis.retransmission || _ws.expert || _ws.malformed) && frame.len > 44 && frame.len < 1400 && !irc contains 20:20:0d:0a && !irc contains 20:20:20:20"`
    * by `python src/prep_deduplicate-trace.py irc_ictf2010-42.pcap --p [N]`

### DNS
* dns_ictf2010.pcap
    * from original file ictf2010.pcap
    * filtered for `dns && !icmp`

* dns_ictf2010_deduped-[N].pcap
    * from ictf2010_dns-f2.pcap
    * by `python src/prep_deduplicate-trace.py dns_ictf2010.pcap --p [N]`
    * filtered by `!_ws.malformed`


### Random
Validation to find structure: Generated PCAPs with no structure (random byte sequences):

generate_random_pcap.py  
with parameters: 

* -l 100
* -c 100 and 10000
* with and without -f


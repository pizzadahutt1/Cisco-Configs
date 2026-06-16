# Cisco-Configs

Cisco Configs used in my homelab. Things like usernames, phone numbers, ip addresses, and passwords have been removed and replaced with placeholders. There are some that are visible, these are ones that are already public like pizzatel phone number(s) and username/passwords. Notes have been added inline on parts that I thought were important to explain their function. 

There's probably a lot of silliness in these configs and unnecessary things. I am by no means an expert, but they seem to work pretty good.

The 3825 and AS5350 configs encompass the entirety of the Pizzatel dialup ISP service. For now.  

Pizzatel is a free to use dialup ISP ran by myself. There are public users listed (dialupuser1/2/3/4), and an ACL in the AS5350 only allows these users access to the internet, nothing else. There are also some internal type users that have unfettered access to my entire network that I use for messing around with dialup locally. 

It does this by setting attributes to each user. Those attributes tell the cisco's AAA service which IP Pool to assign the connection. If you connect to Pizzatel with a public user, you will see you are given a 192.168.3.0 IP. Those users are the only ones given an IP in that subnet, and the ACL does not allow IPs in that subnet access to any other subnet. Internal users are given a different subnet that is not restricted. 

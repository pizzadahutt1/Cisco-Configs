# Cisco-Configs

Cisco Configs used in my homelab. Things like usernames, phone numbers, ip addresses, and passwords have been removed and replaced with placeholders. There are some that are visible, these are ones that are already public like pizzatel phone number(s) and username/passwords. Notes have been added inline on parts that I thought were important to explain their function. 

There's probably a lot of silliness in these configs and unnecessary things. I am by no means an expert, but they seem to work pretty good.

The 3825 and AS5350 configs encompass the entirety of the Pizzatel dialup ISP service. For now.  

Pizzatel is a free to use dialup ISP ran by myself. There are public users listed (dialupuser1/2/3/4), and an ACL in the AS5350 only allows these users access to the internet, or eachother (yes, you and your buddy could both connect to pizzatel and play a LAN game of starcraft if you are both masochists). There are also some internal type users that have unfettered access to my entire network that I use for messing around with dialup locally. 

It does this by setting attributes to each user. Those attributes tell the cisco's AAA service which IP Pool to assign the connection. If you connect to Pizzatel with a public user, you will see you are given a 192.168.3.0 IP. Those users are the only ones given an IP in that subnet, and the ACL does not allow IPs in that subnet access to any other subnet. Internal users are given a different subnet that is not restricted. 

# AS5350 Config

A special note for this config. Recently I ran into an issue where the NVRAM has died on the AS5350. It complains of various things like "NVRAM is 0kb" or "Unable to initialize the geometry of nvram". It will still start, but goes into rommon. Here's how I got around that: 

Once in rommon, you need to get your IOS image onto the flash storage. It should already be there, but if not, you can bring up the ethernet interface in rommon and download it off a TFTP server on your network. 

In rommon, set these:

rommon 1 > IP_ADDRESS=X.X.X.X (the IP you want to assign the cisco)
rommon 2 > IP_SUBNET_MASK=255.255.255.0 (your subnet mask, its probably this already)
rommon 3 > DEFAULT_GATEWAY=X.X.X.X (your other router)

once you do that, you can do:

rommon 4 > copy TFTP: flash:

It will ask you a few details, like the IP of your TFTP server, the file name you want to download, and what you want that file to be named once it is downloaded. 

Once downloaded, you can now boot IOS from rommon

rommon 5 > boot flash:c5350-js-mz.124-25a.bin (or whatever the file name of your IOS image you just downloaded is)

If you can't remember what the file name was, you can see it by looking in the directory of flash:

rommon 5 > dir flash:

IOS will now boot with a standard, empty config

If you planned ahead, you should have a backup of your config saved somewhere else, hopefully on an TFTP server. If not, start doing that. Here's a quick rundown of how to do that (assuming this is a fully functioning router):

router# copy running-config tftp:

That will ask you a few things, like the IP of your TFTP server and the file name you want to save it as

To restore a config from a TFTP server

router# copy tftp: running-config

That will ask you a few things, like the IP of your TFTP server, the file name you want to download, and what you want it to be named (should default to running-config already, keep it that)

In the case of the AS5350 with the bad NVRAM, I basically have to do this every time I reboot it. I saved the config file to the flash so all I have to do is this:

rommon 1 > boot flash:c5350-js-mz.124-25a.bin 

Once it boots 

router# copy flash:as5350-config-071020264 running-config

And I am back up and running. 

I had to totally redo my config to get it to load properly here. Normally the config is loaded at the beginning of the IOS boot, but loading a config after IOS is fully booted can be prickly. In this case, I had to remove the virtual-template interface and multilink virtual-template 1 from the global config. You can compare the previous as5350-config-06152026 to the newer as5350-config-071020264. For some reason, anything to do with virtual-templates causes a continuous memory allocation error. Same with the Group-Async interfaces, now I had to apply those same settings to all 60 Async interfaces individually. Best to do this through a text editor like notepad++ and then save the config to your TFTP server. Multilink still works in this config with no virtual-template as long as ppp multilink is set on all Async interfaces, but it is not very stable. I created a dialer interface, essentially set all the same settings I had in the virtual-template here, and then added "dialer in-band" and "dialer pool-member 1" to each Async interface. That restored the more stable speeds I got with the previous setup, and there is no crazy memory allocation errors flooding the console. 



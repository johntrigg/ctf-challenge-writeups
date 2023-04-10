# Forensics

## picoCTF Wireshark doo dooo do doo...

We are given a pcapng file. Running strings on it doesn't help, so we boot up wireshark. We go and follow the first TCP stream, and look through the various TCP streams, until we find something interesting.

cvpbPGS{c33xno00_1_f33_h_qrnqorrs} Looks to be some sort of encoded text. It's a substition cipher (the curly braces aren't encrypted), and it's simple (the pretext before the curly braces is four lowercase and three upper, just like the flag format picoCTF).

Throw it into cyberchef, first instinct is rot13, which it is, and we have the flag.

## picoCTF Glory of the Garden

Steg challenge. This time, we find the flag by running strings.
## picoCTF Information

Steg challenge. This time, the flag is in the exifdata, encoded in bae64. This can be obtained via exiftool.

## picoCTF Who Is It

Look at the IP addresses, and run whois on the sender IP address. This will get you their identity.

## picoCTF Hideme

It's a steg challenge, which usually involves using the correct tool.

```foremost -i flag.png```

Foremost is a tool that works with png files. It reveals to us some some secret hidden information, namely a zip file. We unzip the file, and are greeted with the flag.

## picoCTF PCAP Poison

Search packet bytes for picoCTF, and the flag will pop up.

## picoCTF Find and Open

We are given a PCAP file and a zip file. When we run strings on the PCAP file, we get what appears to be a base64 encoded string (it ends with an = sign, and consists of only numbers and letters). 

However, when we put it into cyberchef, it doesn't decode. We open it up in wireshark, and look around. We have a few things: some ethernet data, then some chromecast DNS requests, more ethernet, and then more chromecast. Going through the packets quickly, we find that what looked like the base64 string on packet 48.

The reason we couldn't decode it was because it contained extra information - some of the ethernet headers were leaking into the output from strings. When we copy the data as "hex and ascii", and throw it into cyberchef, cyberchef properly autodetects, decodes, and gets us half of the flag.

We are given a password protected zip, and given it can't be bruteforced (I tried with zip2john), we should try the password that we were given, which is also half of the flag. This successfully unlocks the zip file, and gives us the full flag.
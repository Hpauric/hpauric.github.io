---
published: false
---
## A New Post


```shell
pscp -i "C:\Users\Pauric\downloads\putty-private-key.ppk" root@18.218.102.13:/usr "C:\Users\Pauric\
downloads"
```
Problem:
Fatal: Received unexpected end-of-file from server

What exactly is pscp?

I gotta admit, the PUTTY documentation is pretty amazing.

https://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter5.html

Their definition of a public and private key is really illuminating:

> Public key authentication is an alternative means of identifying yourself to a login server, instead of typing a password. It is more secure and more flexible, but more difficult to set up.

> In conventional password authentication, you prove you are who you claim to be by proving that you know the correct password. The only way to prove you know the password is to tell the server what you think the password is. This means that if the server has been hacked, or spoofed (see section 2.2), an attacker can learn your password.

> Public key authentication solves this problem. You generate a key pair, consisting of a public key (which everybody is allowed to know) and a private key (which you keep secret and do not give to anybody). The private key is able to generate signatures. A signature created using your private key cannot be forged by anybody who does not have that key; but anybody who has your public key can verify that a particular signature is genuine.

> So you generate a key pair on your own computer, and you copy the public key to the server. Then, when the server asks you to prove who you are, PuTTY can generate a signature using your private key. The server can verify that signature (since it has your public key) and allow you to log in. Now if the server is hacked or spoofed, the attacker does not gain your private key or password; they only gain one signature. And signatures cannot be re-used, so they have gained nothing.

> There is a problem with this: if your private key is stored unprotected on your own computer, then anybody who gains access to that will be able to generate signatures as if they were you. So they will be able to log in to your server under your account. For this reason, your private key is usually encrypted when it is stored on your local machine, using a passphrase of your choice. In order to generate a signature, PuTTY must decrypt the key, so you have to type your passphrase.

It turned out that I needed the full address

ubuntu@ec2-18-218-102-13.us-east-2.compute.amazonaws.com

-i flag for private key. the -i flag must immediately be followed by the key file
-r flag to include directories
wildcard * to include all sub-directories and files

pscp -i C:\Users\Pauric\downloads\putty-private-key.ppk -r ubuntu@ec2-18-218-102-13.us-east-2.compute.amazonaws.com:/usr/* C:\

listing the contents of my workspace?

```shell
pscp -i C:\Users\Pauric\downloads\putty-private-key.ppk -ls ubuntu@ec
2-18-218-102-13.us-east-2.compute.amazonaws.com:/home/ubuntu/steem-docker
```
```shell
pscp -i C:\Users\Pauric\downloads\putty-private-key.ppk -r ubuntu@ec2
-18-218-102-13.us-east-2.compute.amazonaws.com:/home/ubuntu/steem-docker/data/witness_node
_data_dir/config.ini.example C:\witness\config2
```
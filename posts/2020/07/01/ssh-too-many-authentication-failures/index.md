# SSH Too Many Authentication Failures


Recently I have been working on our self-managed Kubernetes cluster, specifically tasked with exposing the etcd metrics
endpoint to our worker nodes so that our Prometheus server can retrieve the appropriate metrics. While testing some changes
to the tectonic installer, I hit an issue I've never seen before

`Received disconnect from XXX.XXX.XXX.XXX port 22:2: Too many authentication failures`

Since my task had me inspecting both control-plane and etcd instances to ensure their unit files were being correctly executed,
and the only way I could SSH onto an etcd node was via the control-plane, it was necessary to setup agent forwarding
to ssh from the control-plane to the etcd node. Doing so normally looks something like this:
* Set correct permissions on your private key
* Find control-plane and etcd node IP addresses
* SSH to control-plane as the `core` user (we're using CoreOS) making sure to forward your agent `-A`
* Once on the control-plane, SSH onto the etcd node, again as the `core` user

```
15:20:04 ------------------------------------------------------------
» chmod 0600 generated/private-key.pem

15:20:09 ------------------------------------------------------------
» ssh-add generated/private-key.pem
Identity added: generated/private-key.pem (generated/private-key.pem)

15:20:43 ------------------------------------------------------------
» ssh core@10.128.10.195 -i generated/private-key.pem -A
Received disconnect from 10.128.10.195 port 22:2: Too many authentication failures
Disconnected from 10.128.10.195 port 22
```
What?! Too many authentication failures?

At first I thought that maybe the control-plane node was still coming up, so I waited a little longer...
```
15:21:32 ------------------------------------------------------------
» ssh core@10.128.10.195 -i generated/private-key.pem -A
Received disconnect from 10.128.10.195 port 22:2: Too many authentication failures
Disconnected from 10.128.10.195 port 22
```

That didn't seem to work, so I decided to check what was visible to my `ssh-agent`
```
15:23:01 ------------------------------------------------------------
» ssh-add -L
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
ssh-rsa AAAAB3N... generated/private-key.pem
```

I've been bringing up and taking down these clusters quite a bit, each time adding a new key with the same name
to my `ssh-agent`, could the agent be going through each key and trying them in turn thereby failing before it reaches
the right one?

I know the `ssh-agent` will "forget" my keys on reboot, but I've not rebooted in a while...Guess it's time to empty out
the listed identities
```
15:23:05 ------------------------------------------------------------
» ssh-add -h
ssh-add: illegal option -- h
usage: ssh-add [options] [file ...]
Options:
  -l          List fingerprints of all identities.
  -E hash     Specify hash algorithm used for fingerprints.
  -L          List public key parameters of all identities.
  -k          Load only keys and not certificates.
  -c          Require confirmation to sign using identities
  -m minleft  Maxsign is only changed if less than minleft are left (for XMSS)
  -M maxsign  Maximum number of signatures allowed (for XMSS)
  -t life     Set lifetime (in seconds) when adding identities.
  -d          Delete identity.
  -D          Delete all identities.
  -x          Lock agent.
  -X          Unlock agent.
  -s pkcs11   Add keys from PKCS#11 provider.
  -e pkcs11   Remove keys provided by PKCS#11 provider.
  -T pubkey   Test if ssh-agent can access matching private key.
  -q          Be quiet after a successful operation.
  -A          Add all identities stored in your keychain.
  -K          Store passphrases in your keychain.
              With -d, remove passphrases from your keychain.
```

Ok, so the `-h` flag isn't valid, but I found what I needed `-D Delete all identities.`
```
15:23:17 ------------------------------------------------------------
» ssh-add -D
All identities removed.
```

Right, let's try this again
```
15:23:22 ------------------------------------------------------------
» ssh-add generated/private-key.pem
Identity added: generated/private-key.pem (generated/private-key.pem)

15:23:26 ------------------------------------------------------------
» ssh core@10.128.10.195 -i generated/private-key.pem -A
Container Linux by CoreOS stable (2512.3.0)

CoreOS Container Linux is no longer maintained or updated.
Migrate your workloads to another operating system!
For more info, see https://coreos.com/os/eol/

Update Strategy: No Reboots
core@ip-10-128-10-195 ~ $
```

Success 🥳

And with that, thanks for reading.


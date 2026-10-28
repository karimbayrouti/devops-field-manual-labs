# SSH into myself (Module 1, Lab 1.5)

## The idea
SSH is a secure remote command line. The server keeps my public key (the padlock) and I keep the private key. I practiced on my own machine, so it cost nothing.

## Commands
```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
ssh-keygen -t ed25519 -C "lab key"
ssh-copy-id localhost
ssh localhost
echo $SSH_CONNECTION
exit
```
On the first connection SSH showed the server fingerprint. I compared it with the ED25519 fingerprint printed during the install before typing yes. Inside the session, echo $SSH_CONNECTION printed four values (my IP, my port, the server IP, the server port 22). Outside the session it printed nothing.

## What I broke on purpose
chmod 644 on the private key made SSH print UNPROTECTED PRIVATE KEY FILE, ignore the key and fall back to asking for a password. chmod 600 fixed it. A private key that others can read is not private. The password fallback is why real servers turn password login off.

## Cleanup
```bash
sudo systemctl disable --now ssh ssh.socket
systemctl is-active ssh ssh.socket
systemctl is-enabled ssh ssh.socket
sudo ss -tlnp | grep :22
```
Both services showed inactive and disabled, and nothing was listening on port 22.

# Module 1 exercises

## 1.1 Paths and moves
```bash
cd /var/log
cd -
mkdir -p app/src app/tests
cp README.md app/
```
Brace version: mkdir -p app/{src,tests}. The comma inside the braces is what makes it work.

## 1.2 Read the permissions
The file -rw-r----- is 640. To let the owner also execute it, use 740.

## 1.3 Service investigation (service called myapp)
```bash
systemctl status myapp
systemctl is-enabled myapp
sudo journalctl -u myapp -b --no-pager | tail -50
sudo systemctl enable --now myapp
systemctl status myapp
```
Running and enabled are different. A service that was started by hand but never enabled disappears after a reboot.

## 1.4 Log detective
See log-detective.md.

## 1.5 Disk is full
```bash
df -h
sudo du -sh /var/* 2>/dev/null | sort -rh | head
sudo find / -xdev -type f -size +500M 2>/dev/null
sudo truncate -s 0 /var/log/myapp/huge.log
```
Use truncate instead of rm on a log that a running program is writing to. rm removes the name but the program keeps the file open, so the space is not freed.

## 1.6 Top paths with errors
```bash
awk '$9 >= 400 {print $7}' access.log | sort | uniq -c | sort -rn
awk '{total++} $9 >= 400 {err++} END {printf "%.1f%%\n", 100*err/total}' access.log
```
9 error lines out of 20 requests gives 45.0%.

## Mistakes I keep making
- Forgetting sort before uniq -c.
- Using the same column for the filter and the print. The filter used the status (word 9) and the print used the path (word 7).
- Not reading the question words. "How many" wants a total, "each, with a count" wants a ranked list.
- Stray spaces in flags, like wc - l or - size.
- Hiding errors with 2>/dev/null and then trusting an empty result. Check with echo $?.

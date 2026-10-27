# Incident report: nginx would not restart (Module 1, Lab 1.4)

## What happened
I deleted one semicolon from line 22 of /etc/nginx/sites-enabled/default on purpose, then restarted nginx. The restart failed and the website went down.

## How I found it
```bash
sudo systemctl status nginx --no-pager
sudo journalctl -u nginx --no-pager | tail -20
sudo nginx -t
```
The error said: invalid parameter "listen" in /etc/nginx/sites-enabled/default:23

## Root cause
The semicolon was missing on line 22. nginx only noticed on line 23, so the real mistake was one line above the line in the error message.

## Fix
I put the semicolon back, then proved the file matched my backup copy.
```bash
sudo nginx -t
diff ~/nginx-default.bak /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
curl -I http://localhost
```
Result: syntax is ok, diff printed nothing, and curl returned HTTP/1.1 200 OK.

## What I would do in real life
Run nginx -t before every restart. Make a backup copy before editing a config file. Prefer reload over restart when the service is already running, because a bad config makes reload fail while the old config keeps serving visitors.

## Bonus: reading the access log
The awk pipelines from the log lab also work on /var/log/nginx/access.log. It had 12 lines: 9 returned 200, 1 returned 304 and 2 returned 404 (the browser asking for favicon.ico). The counts added up to wc -l, so the analysis can be trusted.

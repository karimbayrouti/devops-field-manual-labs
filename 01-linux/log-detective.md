# Log detective (Module 1, Lab 1.3)

Log file: access.log (20 lines). Word 1 is the IP, word 7 is the path, word 9 is the status code.

## Q1: How many times did each status code appear?
```bash
awk '{print $9}' access.log | sort | uniq -c
```
Answer: 200 appeared 11 times, 401 three times, 403 twice, 404 once, 500 twice, 502 once.

## Q2: Which IP sent the most requests?
```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn
```
Answer: 192.168.1.10 with 5 requests.

## Q3: How many server errors (500 to 599)?
```bash
awk '$9 >= 500 && $9 <= 599' access.log | wc -l
```
Answer: 3.

## Q4: Which IP failed to log in?
```bash
awk '$7 == "/login" && $9 > 200 {print $1}' access.log | sort | uniq -c
```
Answer: 10.0.0.5 failed 3 times.

## Q5: How many requests were not health checks?
```bash
awk '$7 != "/health" {print $1}' access.log | wc -l
```
Answer: 16.

## What I learned
- Always sort before uniq -c, because uniq only counts neighbours that are identical.
- Read the last words of the question. "How many" wants a total, "which" wants a ranking.
- Sanity check the total. If the numbers don't add up to the line count, something is wrong.

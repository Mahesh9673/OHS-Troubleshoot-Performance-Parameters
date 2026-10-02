# OHS-Troubleshoot-Performance-Parameters



OHS Commands:

ps -eo pid,ppid,%cpu,%mem,rss,vsz,etime,cmd | grep httpd

vmstat 5

mpstat -P ALL 5

curl -s http://localhost:<port>/server-status?auto

OHS configuration parameters:
grep -v '^[[:space:]]*#' <OHS_CONFIG>/httpd.conf

MPM :
StartServers
MinSpareServers
MaxSpareServers
MaxRequestWorkers
ServerLimit
ThreadsPerChild
MaxConnectionsPerChild


awk '$9 ~ /^5/ {count++} END {print count}' access_log
awk '$9 == 500 {count++} END {print "HTTP 500:",count}' access_log

Slow URLS:
awk '{print $NF, $7}' access_log | sort -nr | head -20

File descriptor usage:
OHS can be affected by file/socket limits.
Check:
ulimit -n

For an OHS process:
cat /proc/<PID>/limits | grep "open files"

Current open descriptors:
ls /proc/<PID>/fd | wc -l

System-wide:
cat /proc/sys/fs/file-nr

Check End-to-End response Time:
curl -o /dev/null -s -w \
"DNS: %{time_namelookup}\nConnect: %{time_connect}\nTLS: %{time_appconnect}\nTTFB: %{time_starttransfer}\nTotal: %{time_total}\nHTTP: %{http_code}\n" \
https://<OHS_HOST>/<URL>

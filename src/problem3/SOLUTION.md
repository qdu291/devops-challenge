Provide your solution here:
When a alert is fired for Disk usage 99%, should immediately identify what is consuming disk space before instance completely locks up
Identify large items on disk
    Using command `du -ahx / | sort -rh | head -n 20` # prints the top 20 largest items

Because this server is used to host Nginx Load Balancer server so below are some of root cause related with Nginx

## Nginx log
- **Root cause:** access and error log `/var/log/nginx/access.log` or `/var/log/nginx/error.log` are too big
- **Fix:** 
1. If there is logrotate configured, check to make sure it's configured correctly, then trigger it to make sure it run as expectation and also to free disk
2. If we need keep log for long term, we can setup Nginx to save log to remote log server or stream to logging platform such as `Grafana Loki`, `Elasticsearch`, `Cloudwatch`, etc.

## Nginx proxy cache
- **Root cause:** Nginx is configured to cache response from upstream servers, but missing set `max_size` parameter then make `/var/cache/nginx` consume lot of disk space
- **Fix:**
1. Manually clear `sudo rm -rf /var/cache/nginx/*`, then reload nginx
2. Configure Nginx cache size limit (ref: https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/#enable-the-caching-of-responses)

## Systemd journal logs
- **Root cause** log in `/var/log/journal/` is too big
- **Fix:**
1. Clean up journal log by using `sudo journalctl --vacuum-size=1G` -> keep only 1G
2. Configure maximum size limit `SystemMaxUse=2G`

## APT Cache
- **Root cause:** Ubuntu downloads updates via `apt` and not delete automatically
- **Fix:** `sudo apt-get clean`

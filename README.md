# tech_notes

Here's a simple Bash script that uses curl's built-in timing variables to measure:
DNS lookup time
TCP connection time
TLS handshake time
Time to first byte (TTFB)
Total page load time
#!/bin/bash

URL=${1:-https://www.google.com}

echo "Testing: $URL"
echo "-------------------------------------"

curl -o /dev/null -s -w "
DNS Lookup Time      : %{time_namelookup}s
TCP Connect Time     : %{time_connect}s
TLS Handshake Time   : %{time_appconnect}s
TTFB                 : %{time_starttransfer}s
Total Response Time  : %{time_total}s
Remote IP            : %{remote_ip}
HTTP Status          : %{http_code}
\n" "$URL"
Usage
chmod +x curl-timing.sh

./curl-timing.sh https://www.wayfair.com
Example output:
Testing: https://www.wayfair.com
-------------------------------------

DNS Lookup Time      : 0.012345s
TCP Connect Time     : 0.045678s
TLS Handshake Time   : 0.123456s
TTFB                 : 0.456789s
Total Response Time  : 0.678901s
Remote IP            : 151.101.1.124
HTTP Status          : 200
Continuous Monitoring Version
If you'd like to monitor a site every 30 seconds:
#!/bin/bash

URL=${1:-https://www.google.com}

while true; do
    echo "$(date)"
    curl -o /dev/null -s -w \
    "DNS:%{time_namelookup}s TCP:%{time_connect}s TLS:%{time_appconnect}s TTFB:%{time_starttransfer}s TOTAL:%{time_total}s HTTP:%{http_code}\n" \
    "$URL"

    sleep 30
done
DNS Resolution Only
If you're specifically interested in DNS performance:
#!/bin/bash

HOST=$1

curl -o /dev/null -s -w "DNS Lookup Time: %{time_namelookup}s\n" "https://$HOST"
For network troubleshooting, especially with Zscaler or proxy environments, it's often useful to compare:
# Direct
./curl-timing.sh https://www.example.com

# Through proxy
curl -x http://proxy.company.com:8080 -o /dev/null -s -w \
"DNS:%{time_namelookup}s TCP:%{time_connect}s TLS:%{time_appconnect}s TTFB:%{time_starttransfer}s TOTAL:%{time_total}s\n" \
https://www.example.com
This quickly shows whether latency is coming from DNS, connection establishment, TLS negotiation, or server response time.


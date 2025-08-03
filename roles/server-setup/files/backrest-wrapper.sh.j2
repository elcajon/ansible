#!/bin/bash
for i in {1..20}; do
  ip=$(/usr/bin/tailscale ip --4 | head -n1)
  if [ -n "$ip" ]; then
    exec /usr/local/bin/backrest --bind-address "${ip}:9898"
  fi
  sleep 1
done
echo "No Tailscale IP found, aborting." >&2
exit 1

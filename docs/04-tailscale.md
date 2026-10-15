# Tailscale Remote Access

Tailscale provides private remote access without exposing SMB or management ports directly to the public Internet.

## LXC requirement

The Ubuntu container required access to `/dev/net/tun` for Tailscale networking.

## Result

With Tailscale connected, authorized devices can securely reach the file server from outside the home network.

> Real Tailscale IP addresses and authentication details are not published.

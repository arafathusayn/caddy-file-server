### Static File Server with Directory Listing in Caddy
Example configuration for Caddy File Server that denies access via Public IP address, only allowing Private IP access

> `Caddyfile`
```
{
	auto_https off
	admin off
}

:5001 {
	root /path/to/your/public/directory

	@allowed_networks {
		remote_ip private_ranges 127.0.0.1/8 172.16.0.0/12 192.168.0.0/16
	}

	@public_ip {
		not remote_ip private_ranges 127.0.0.1/8 172.16.0.0/12 192.168.0.0/16
	}

	handle @allowed_networks {
		file_server / browse
	}

	handle @public_ip {
		respond "Access denied" 403
	}
}
```

> Command:

```
tmux
caddy run
```

Then you may setup reverse proxy for port `5001`, e.g. `http://127.0.0.1:5001` with your domain names using other tools like `nginx-proxy-manager`. Make sure you use host network mode in Docker:

```yaml
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    #ports:
      #- '80:80'
      #- '81:81'
      #- '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    network_mode: host
```

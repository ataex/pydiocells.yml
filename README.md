# pydiocells.yml

Pydio Cells in Docker Swarm, with Traefik (which also is handling certificates).

Took me a while to get this stack to work, so I'm putting it here to benefit someone else who's stuck.

The major issues were: 

I would setup the software, but upon restart the setup did not persist.  Had to mount this volume:

`config:/root/.config`

Docker Swarm gives a new IP address when the stack would come up, therefore data sources always had incorrect Peer Addresses.  This is a hack, but solves the issue:

`command: [ '/bin/sh', '-c', 'echo `tail -2 /etc/hosts | head -1 | cut -f 1` > /home/ip.txt; sed -i "/PeerAddress/ s/: .*/: \"`cat /home/ip.txt`\",/g" /root/.config/pydio/cells/pydio.json; exec /bin/docker-entrypoint.sh cells start' ]`

I struggled to get the proper NFS driver syntax (Docker issue not Pydio), and also the correct hierarchy with the mount point from Pydio's perspective.

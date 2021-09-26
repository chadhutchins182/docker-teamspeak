# chadhutchins182/docker-teamspeak

_Based off work from mbentley/docker-teamspeak_

<div align="center">
<hr>
<br>

[![Status](https://img.shields.io/badge/status-active-success.svg)](https://github.com/chadhutchins182/docker-teamspeak/)
[![Build and Push](https://github.com/chadhutchins182/docker-teamspeak/actions/workflows/buildandpush.yml/badge.svg)](https://github.com/chadhutchins182/docker-teamspeak/actions/workflows/buildandpush.yml)
[![Docker Pulls](https://img.shields.io/static/v1?label=Container%20Registry&message=GitHub&color=blue)](https://hub.docker.com/r/chadhutchins182/docker-teamspeak)
[![GitHub Issues](https://img.shields.io/github/issues/chadhutchins182/docker-teamspeak.svg)](https://github.com/chadhutchins182/docker-teamspeak/issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/chadhutchins182/docker-teamspeak.svg)](https://github.com/chadhutchins182/docker-teamspeak/pulls)
<br>

<hr>
</div>

Docker image for TeamSpeak 3 Server based off of alpine:latest

- `latest`, `alpine` - based on Alpine Linux

To pull this image:
`docker pull ghcr.io/chadhutchins182/docker-teamspeak:latest`

Note: This Dockerfile will always install the very latest version of TS3 available.

Example usage (no persistent storage; for testing only - you will lose your data when the container is removed):

```
docker run -d --name teamspeak \
  -e TS3SERVER_LICENSE=accept \
  -p 9987:9987/udp -p 30033:30033 -p 10011:10011 -p 41144:41144 \
  ghcr.io/chadhutchins182/docker-teamspeak:latest
```

## Set User ID and Group ID

Setting the host user id and group id can help with permission issues on the host system with persistent storage and if running as non-root.

To set the UID and GID:

```
-e PUID=1000 -e PGID=1000
```

## License Agreement

Starting with [TeamSpeak 3.1.0](https://support.teamspeakusa.com/index.php?/Knowledgebase/Article/View/344/16/how-to-accept-the-server-license-agreement-server--310), the license agreement must be accepted before you can run TeamSpeak. To accept the agreement, you need to do one of the following:

- Add the following to your run command: `-e TS3SERVER_LICENSE=accept` to pass an environment variable
- Add a command line parameter at the end of your run command to accept the license `license_accepted=1`
- Create a file named `.ts3server_license_accepted` in the persistent storage directory

## Credentials on a New Setup

In order to get the credentials for your TS server, check the container logs as it will output the `serveradmin` password and your `ServerAdmin` privilege key.

## Additional Parameters

For additional parameters, check the `Commandline Parameters` section of the `TeamSpeak Server - Quickstart Guide`. You can also get Either add the parameters to `ts3server.ini` or specify them after the Docker image name. The quickstart guide ships with the image and can be viewed with:

```
docker run -t --rm --entrypoint cat ghcr.io/chadhutchins182/docker-teamspeak:latest /opt/teamspeak/doc/server_quickstart.md
```

## Example directly passing parameters

```
docker run -d --restart=always --name teamspeak \
  -e TS3SERVER_LICENSE=accept \
  -p 9987:9987/udp -p 30033:30033 -p 10011:10011 -p 41144:41144 \
  -v /data/teamspeak:/data \
  ghcr.io/chadhutchins182/docker-teamspeak:latest \
  serveradmin_password=test1234
```

## Use a custom .ini file

1. Create directory, touch the ini files, set permissions:

   ```
   mkdir -p /data/teamspeak
   touch /data/teamspeak/ts3server.ini
   chown -R 503:503 /data/teamspeak
   ```

1. Add config parameters to the `ts3server.ini`:

   ```
   default_voice_port=9987
   filetransfer_ip=0.0.0.0
   filetransfer_port=30033
   query_port=10011
   query_ssh_port=10022
   ```

1. Start container with the `inifile=/data/ts3server.ini` parameter to tell TeamSpeak where the ini file is:

   ```
   docker run -d --restart=always --name teamspeak \
     -e TS3SERVER_LICENSE=accept \
     -p 9987:9987/udp -p 30033:30033 -p 10011:10011 -p 41144:41144 \
     -v /data/teamspeak:/data \
     ghcr.io/chadhutchins182/docker-teamspeak:latest \
     inifile=/data/ts3server.ini
   ```

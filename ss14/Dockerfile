# based on https://docs.spacestation14.io/getting-started/hosting
FROM bitnami/dotnet-sdk:5 AS build

ADD https://github.com/space-wizards/SS14.Watchdog/archive/refs/heads/master.zip /tmp/

RUN unzip /tmp/master.zip -d /tmp/ && mv /tmp/SS14.Watchdog-master/SS14.Watchdog/ /ss14

RUN cd /ss14 && dotnet publish -c Release -r linux-x64 --self-contained true /ss14

FROM ubuntu:21.04 AS server

# https://github.com/dotnet/core/issues/2186
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT 1

COPY --from="build" /ss14/bin/Release/net5.0/linux-x64/publish/ /ss14

EXPOSE "1212/tcp"
EXPOSE "1212/udp"
EXPOSE "5000/tcp"
EXPOSE "5000/udp"

VOLUME [ "/data" ] 

RUN cat <<-EOF >/ss14/appsettings.yml
Logging:
  LogLevel:
    Default: "Information"
    Microsoft: "Warning"
    Microsoft.Hosting.Lifetime: "Information"
    SS14: "Debug"

AllowedHosts: "*"

# API URL that your watchdog is accessible from.
# This HAS to be set so the game servers can communicate with the watchdog.
# If you don't want the watchdog to be publically accessible, do `http://localhost:5000/` here.
#BaseUrl: https://your.domain.com/watchdog/
BaseUrl: http://localhost:5000/

Servers:
  Instances:
    # ID of your server.
    test:
      # Name of the server
      Name: "Test Instance"
      ApiToken: "foobar" # API token to control this instance remotely like run updates, restart server.
      ApiPort: 1212 # API port OF THE GAME SERVER. This has to match the 1212 HTTP status API (described below). Otherwise the watchdog can't contact the game server for stuff.

      # Auto update configuration. This can be left out if you do not need auto updates. Example is for our officially hosted builds.
      # See below for alternatives.
      UpdateType: "Manifest"
      Updates:
        ManifestUrl: "https://central.spacestation14.io/builds/wizards/manifest.json"
EOF

CMD cd /ss14 && /ss14/SS14.Watchdog

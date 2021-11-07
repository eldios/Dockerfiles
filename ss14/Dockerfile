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

VOLUME [ "/ss14/instances" ] 

ADD ss14/appsettings.yml /ss14/appsettings.yml

RUN apt-get install ca-certificates

CMD cd /ss14 && /ss14/SS14.Watchdog

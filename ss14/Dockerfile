# based on https://docs.spacestation14.io/getting-started/hosting
FROM bitnami/dotnet-sdk:5 AS build

ADD /ss14 /ss14

WORKDIR /ss14

RUN cd /ss14 && dotnet publish -c Release -r linux-x64 --no-self-contained

FROM alpine AS server

COPY --from="build" /ss14/SS14.Watchdog/bin/Release/netcoreapp3.1/linux-x64/publish/ ss14

EXPOSE ["1212/tcp"]
EXPOSE ["1212/udp"]

VOLUME ["/data"] 

CMD ["/bin/dedi"] 

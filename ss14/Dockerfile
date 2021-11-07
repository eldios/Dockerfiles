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

RUN echo ''                                               > /ss14/appsettings.yml
RUN echo 'Logging:'                                      >> /ss14/appsettings.yml
RUN echo '  LogLevel:'                                   >> /ss14/appsettings.yml
RUN echo '    Default: "Information"'                    >> /ss14/appsettings.yml
RUN echo '    Microsoft: "Warning"'                      >> /ss14/appsettings.yml
RUN echo '    Microsoft.Hosting.Lifetime: "Information"' >> /ss14/appsettings.yml
RUN echo '    SS14: "Debug"'                             >> /ss14/appsettings.yml
RUN echo ''                                              >> /ss14/appsettings.yml
RUN echo 'AllowedHosts: "*"'                             >> /ss14/appsettings.yml
RUN echo ''                                              >> /ss14/appsettings.yml
RUN echo '# API URL your watchdog is accessible from.'   >> /ss14/appsettings.yml
RUN echo '# This NEEDS to be reachable by the watchdog.' >> /ss14/appsettings.yml
RUN echo '# If you don't want the watchdog to be public,'>> /ss14/appsettings.yml
RUN echo '# do `http://localhost:5000/` here.'           >> /ss14/appsettings.yml
RUN echo '#BaseUrl: https://your.domain.com/watchdog/'   >> /ss14/appsettings.yml
RUN echo 'BaseUrl: http://localhost:5000/'               >> /ss14/appsettings.yml
RUN echo ''                                              >> /ss14/appsettings.yml
RUN echo 'Servers:'                                      >> /ss14/appsettings.yml
RUN echo '  Instances:'                                  >> /ss14/appsettings.yml
RUN echo '    # ID (and directory) of your server.'      >> /ss14/appsettings.yml
RUN echo '    test:'                                     >> /ss14/appsettings.yml
RUN echo '      # Name of the server'                    >> /ss14/appsettings.yml
RUN echo '      Name: "Test Instance"'                   >> /ss14/appsettings.yml
RUN echo '      # Token to control the instance remotely'>> /ss14/appsettings.yml
RUN echo '      ApiToken: "foobar"'                      >> /ss14/appsettings.yml
RUN echo '      # Port OF THE GAME SERVER.'              >> /ss14/appsettings.yml
RUN echo '      # This should match the HTTP status API' >> /ss14/appsettings.yml
RUN echo '      # or watchdog can't contact the server.' >> /ss14/appsettings.yml
RUN echo '      ApiPort: 1212'                           >> /ss14/appsettings.yml
RUN echo ''                                              >> /ss14/appsettings.yml
RUN echo '      # Auto update configuration. This can be'>> /ss14/appsettings.yml
RUN echo '      # omitted to skip auto updates.'         >> /ss14/appsettings.yml
RUN echo '      UpdateType: "Manifest"'                  >> /ss14/appsettings.yml
RUN echo '      Updates:'                                >> /ss14/appsettings.yml
RUN echo '        ManifestUrl: "https://central.spacestation14.io/builds/wizards/manifest.json"' >> /ss14/appsettings.yml

CMD cd /ss14 && /ss14/SS14.Watchdog

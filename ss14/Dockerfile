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

RUN cat ''                                               > /ss14/appsettings.yml
RUN cat 'Logging:'                                      >> /ss14/appsettings.yml
RUN cat '  LogLevel:'                                   >> /ss14/appsettings.yml
RUN cat '    Default: "Information"'                    >> /ss14/appsettings.yml
RUN cat '    Microsoft: "Warning"'                      >> /ss14/appsettings.yml
RUN cat '    Microsoft.Hosting.Lifetime: "Information"' >> /ss14/appsettings.yml
RUN cat '    SS14: "Debug"'                             >> /ss14/appsettings.yml
RUN cat ''                                              >> /ss14/appsettings.yml
RUN cat 'AllowedHosts: "*"'                             >> /ss14/appsettings.yml
RUN cat ''                                              >> /ss14/appsettings.yml
RUN cat '# API URL your watchdog is accessible from.'   >> /ss14/appsettings.yml
RUN cat '# This NEEDS to be reachable by the watchdog.' >> /ss14/appsettings.yml
RUN cat '# If you don't want the watchdog to be public,'>> /ss14/appsettings.yml
RUN cat '# do `http://localhost:5000/` here.'           >> /ss14/appsettings.yml
RUN cat '#BaseUrl: https://your.domain.com/watchdog/'   >> /ss14/appsettings.yml
RUN cat 'BaseUrl: http://localhost:5000/'               >> /ss14/appsettings.yml
RUN cat ''                                              >> /ss14/appsettings.yml
RUN cat 'Servers:'                                      >> /ss14/appsettings.yml
RUN cat '  Instances:'                                  >> /ss14/appsettings.yml
RUN cat '    # ID (and directory) of your server.'      >> /ss14/appsettings.yml
RUN cat '    test:'                                     >> /ss14/appsettings.yml
RUN cat '      # Name of the server'                    >> /ss14/appsettings.yml
RUN cat '      Name: "Test Instance"'                   >> /ss14/appsettings.yml
RUN cat '      # Token to control the instance remotely'>> /ss14/appsettings.yml
RUN cat '      ApiToken: "foobar"'                      >> /ss14/appsettings.yml
RUN cat '      # Port OF THE GAME SERVER. 
RUN cat '      # This should match the HTTP status API' >> /ss14/appsettings.yml
RUN cat '      # or watchdog can't contact the server.' >> /ss14/appsettings.yml
RUN cat '      ApiPort: 1212'                           >> /ss14/appsettings.yml
RUN cat ''                                              >> /ss14/appsettings.yml
RUN cat '      # Auto update configuration. This can be'>> /ss14/appsettings.yml
RUN cat '      # omitted to skip auto updates.'         >> /ss14/appsettings.yml
RUN cat '      UpdateType: "Manifest"'                  >> /ss14/appsettings.yml
RUN cat '      Updates:'                                >> /ss14/appsettings.yml
RUN cat '        ManifestUrl: "https://central.spacestation14.io/builds/wizards/manifest.json"' >> /ss14/appsettings.yml

CMD cd /ss14 && /ss14/SS14.Watchdog

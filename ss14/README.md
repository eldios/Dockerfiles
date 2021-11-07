# ss14-server - Space Station 14 Dedicated Server

This image runs the [Official Watchdog](https://github.com/space-wizards/SS14.Watchdog/) wrapper to run a `Space Station 14`
Dedicated Server entirely in one container.

[Docker Hub page](https://hub.docker.com/repository/docker/eldios/ss14-server)

[GitHub page](https://github.com/eldios/Dockerfiles/tree/master/ss14)

## USAGE

The container image is set to start a server called `test` by default. 
Simply run:
```
docker run -p 1212:1212/tcp -p 1212:1212/udp -p 5000:5000/tcp -p 5000:5000/udp -v $HOME/ss14_instances:/ss14/instances eldios/ss14-server
```

You can override this by overwriting the file `/ss14/appsettings.yml` to insert your own configuration.
This the default content of that file:

```
Logging:
  LogLevel:
    Default: "Information"
    Microsoft: "Warning"
    Microsoft.Hosting.Lifetime: "Information"
    SS14: "Debug"

Serilog:
  Using: [ "Serilog.Sinks.Console" ]
  MinimumLevel:
    Default: Information
    Override:
      SS14: Information
      Microsoft: "Warning"
      Microsoft.Hosting.Lifetime: "Information"
      Microsoft.AspNetCore: Warning

  WriteTo:
    - Name: Console
      Args:
        OutputTemplate: "[{Timestamp:HH:mm:ss} {Level:u3} {SourceContext}] {Message:lj}{NewLine}{Exception}"

  Enrich: [ "FromLogContext" ]

AllowedHosts: "*"

# API URL your watchdog is accessible from.
# This NEEDS to be reachable by the watchdog.
# If you don't want the watchdog to be public,
# do `http://localhost:5000/` here.
#BaseUrl: https://your.domain.com/watchdog/
BaseUrl: http://localhost:5000/

Servers:
  Instances:
    # ID (and directory) of your server.
    test:
      # Name of the server
      Name: "Test Instance"
      # Token to control the instance remotely
      ApiToken: "foobar"
      # Port OF THE GAME SERVER.
      # This should match the HTTP status API
      # or watchdog can't contact the server.
      ApiPort: 1212

      # Auto update configuration. This can be
      # omitted to skip auto updates.
      UpdateType: "Manifest"
      Updates:
        ManifestUrl: "https://central.spacestation14.io/builds/wizards/manifest.json"
```

## VOLUMES

All your instances will be saved by default in `/ss14/instances` so mount that directory outside of the container to save your servers' content.

## TODO
* Add Docker-Compose file
* Add Helm Chart

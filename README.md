# use serilog by config

[identity server quickstart demo](https://github.com/IdentityServer/IdentityServer4/tree/master/samples/Quickstarts/9_Combined_AspId_and_EFStorage) use hard code to config serilog.

```
       public static IWebHostBuilder CreateWebHostBuilder(string[] args)
        {
            return WebHost.CreateDefaultBuilder(args)
                    .UseStartup<Startup>()
                    .UseSerilog((context, configuration) =>
                    {
                        configuration
                            .MinimumLevel.Debug()
                            .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
                            .MinimumLevel.Override("System", LogEventLevel.Warning)
                            .MinimumLevel.Override("Microsoft.AspNetCore.Authentication", LogEventLevel.Information)
                            .Enrich.FromLogContext()
                            .WriteTo.File(@"identityserver4_log.txt")
                            .WriteTo.Console(outputTemplate: "[{Timestamp:HH:mm:ss} {Level}] {SourceContext}{NewLine}{Message:lj}{NewLine}{Exception}{NewLine}", theme: AnsiConsoleTheme.Literate);
                    });
        }
```

I would like to change it to use  appsetting.json to do the same thing



program.cs

```
namespace IdentityServerAspNetIdentity
{
    public class Program
    {
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .UseSerilog((ctx, cfg) => {
                    cfg.ReadFrom.Configuration(ctx.Configuration);
                });
    }
}
```

appsettings.json

```
{
  "Serilog": {
    "Using": [ "Serilog.Sinks.Console" ],
    "MinimumLevel": "Information",
    "Override": {
      "Microsoft": "Warning",
      "Microsoft.AspNetCore.Authentication": "Information",
      "System": "Warning"
    },
    "WriteTo": [
      {
        "Name": "Console",
        "Args": {
          "outputTemplate": "[{Timestamp:HH:mm:ss} {Level}] {SourceContext}{NewLine}{Message:lj}{NewLine}{Exception}{NewLine}",
          "theme": "Serilog.Sinks.SystemConsole.Themes.AnsiConsoleTheme::Literate,Serilog.Sinks.Console"
        }
      },
      {
        "Name": "File",
        "Args": { "path": "identityserver.log" }
      }
    ],
    "Enrich": [ "FromLogContext" ],
    "Properties": {
      "Application": "Identity Server"
    }
  }
}
```






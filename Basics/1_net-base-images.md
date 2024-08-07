# Base Images
The base image is the image you use in the FROM line in your Dockerfile. It was built with a Dockerfile and it comes packaged with software:

nginx is a Linux image with the web server installed
nanoserver is a Windows image with just the OS installed
aspnet is a Windows image with IIS and ASP.NET installed
Microsoft maintain Docker images for the .NET stack on their own registry, mcr.microsoft.com.

## .NET Core image

Pulling runtime images
    - Just has .NET Core installed.
    - We can use that to run an application that is pre-compiled
    - Doesn't have SDK so we can't compile the application
    - Doesn't have ASP.NET Runtime, so we can't run application
    - This will be useful if we want to run Console applications/Background apps/Batch processing etc. that are pre-compiled

TaskContainer
docker run mcr.microsoft.com/dotnet/core/runtime:3.1 dotnet --list-runtimes
-- This command uses .NET CLI to list all runtimes installed

Pulling SDK image
    - SDK image has all the MSBuild tools
    - Using that we can compile our code

If we do not have an image locally and if we execute run command for it, it will download that image first and then execute it.

docker run mcr.microsoft.com/dotnet/core/sdk:3.1 dotnet --list-sdks
    -Downloads SDK 3.1 image and then using .NET CLI to run commands

Pulling ASP.NET Core image
    - Builds on runtime image and adds web server runtime.
    - IMP - Recommendation - We should ideally use smallest image to give us what we need.
        - For Console app -> It should be based on runtime image
        - For WebAPI -> It should be based on aspnet image

## Comparing all the images
    docker image ls mcr.microsoft.com/dotnet/core/*:3.1

    mcr.microsoft.com/dotnet/core/runtime = Smallest. Around 190MB - Used for Console applications. Based on Linux Debian.
    mcr.microsoft.com/dotnet/core/aspnet = Adds Web runtime to above image. So, its just 18 MBs more. Used for web based applications
    mcr.microsoft.com/dotnet/core/sdk = Biggest. It is full blown SDK. NuGet dependencies, build dependencies and running them.

## Windows images

.NET Core is a cross-platform runtime so you can build apps targeted for Windows or Linux. 
In Docker terms that means you can build Windows and Linux images for .NET Core apps using the same source code.
Switch to Windows container mode and you can pull Windows versions of the .NET Core images.
The Windows images have the same names as the Linux versions:

```docker
docker pull mcr.microsoft.com/dotnet/core/runtime:3.1
docker pull mcr.microsoft.com/dotnet/core/aspnet:3.1
docker pull mcr.microsoft.com/dotnet/core/sdk:3.1
```

There is no difference in the names/tags etc. for windows vs linux based images.
The Docker Daemon would identify its running on which OS and from the image metadata, it will pull the correct version of the image.
So, if the daemon's running on windows, it'd pull the windows based images and vice versa for Linux

The Windows based images are based on "NanoServer" which is a compressed version of Windows Server.
So, they're smaller as compared to an image derived from windowsservercore.
IMP - However, windows based images are 100MB bigger than its linux counterparts.

32 bit and .NET Framework apps can't be run on nanoserver. We can run .NET Core and other cross platform apps based on Java, Node etc. They work fine on NanoServer

.NET Framework 4.8 images
    - All based on Windows Server Core. 
    - Much bigger than .NET Core variants.
    - They can only run on Windows OS
    - Use IIS as the server

```docker
docker pull mcr.microsoft.com/dotnet/framework/runtime:4.8
docker pull mcr.microsoft.com/dotnet/framework/aspnet:4.8
docker pull mcr.microsoft.com/dotnet/framework/sdk:4.8
```

When it comes to .NET image, it does not contain any sort of template code that comes when we create a new project from Visual studio
Because, the expectation is that no one will run any templated code from a container.
People are supposed to use that as the base image and then build code on top of

Documentation 
Core - https://mcr.microsoft.com/en-us/product/dotnet/aspnet/about
Framework - https://mcr.microsoft.com/en-us/product/dotnet/framework/runtime/about

Repo - https://github.com/dotnet/dotnet-docker

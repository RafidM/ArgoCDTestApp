#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443



FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["./ArgoCDTestApp.csproj", "ArgoCDTestApp/"]
RUN mkdir -p /src/nuget-packages
RUN curl -o /src//nuget-packages/microsoft.visualstudio.azure.containers.tools.targets.1.20.1.nupkg -k https://globalcdn.nuget.org/packages/microsoft.visualstudio.azure.containers.tools.targets.1.20.1.nupkg
ENV NUGET_SOURCE=nuget-packages
RUN ls -l /src/nuget-packages
RUN dotnet restore "./ArgoCDTestApp/ArgoCDTestApp.csproj" --source /src/nuget-packages
WORKDIR "/src/ArgoCDTestApp"
COPY . .
RUN dotnet build --source /src/nuget-packages  "../ArgoCDTestApp/ArgoCDTestApp.csproj" -c $BUILD_CONFIGURATION -o /app/build -r linux-x64

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish --source /src/nuget-packages  "../ArgoCDTestApp/ArgoCDTestApp.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false 

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ArgoCDTestApp.dll"]
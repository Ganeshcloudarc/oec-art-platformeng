FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app
EXPOSE 5039

ENV ASPNETCORE_URLS=http://+:5039

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:7.0 AS build
ARG configuration=Release
WORKDIR /src
COPY ["oec-art-platformeng/payments-api/payments-api.csproj", "oec-art-platformeng/payments-api/"]
RUN dotnet restore "oec-art-platformeng/payments-api/payments-api.csproj"
COPY . .
WORKDIR "/src/oec-art-platformeng/payments-api"
RUN dotnet build "payments-api.csproj" -c $configuration -o /app/build

FROM build AS publish
ARG configuration=Release
RUN dotnet publish "payments-api.csproj" -c $configuration -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "payments-api.dll"]

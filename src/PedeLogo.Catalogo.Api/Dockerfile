#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

# Este exemplo cria em multi-stage build
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
# Este COPY visa o aproveitamento de camadas
COPY ["src/PedeLogo.Catalogo.Api/PedeLogo.Catalogo.Api.csproj", "src/PedeLogo.Catalogo.Api/"]
# O restore baixa todos os pacotes
RUN dotnet restore "src/PedeLogo.Catalogo.Api/PedeLogo.Catalogo.Api.csproj"
# A próxima cópia permite compilar o resto da aplicação
COPY . .
# Workdir para entrar na pasta da API e acessar o arquivo .csproj
WORKDIR "/src/src/PedeLogo.Catalogo.Api"
# Compilar a aplicação baseada no arquivo .csproj
RUN dotnet build "PedeLogo.Catalogo.Api.csproj" -c Release -o /app/build

# Criar um outra imagem de publish para o arquvivo de publicação baseado na imagem build
FROM build AS publish
RUN dotnet publish "PedeLogo.Catalogo.Api.csproj" -c Release -o /app/publish

# Criar a imagem de execução baseada no ASP net core
FROM base AS final
WORKDIR /app
# Copiar os arquivos do publish para o diretório /app
COPY --from=publish /app/publish .
# Executar a aplicação no container
ENTRYPOINT ["dotnet", "PedeLogo.Catalogo.Api.dll"]

# Executar
# docker build -t amisaka/pedelogo-catalogo:v1.0.0 -f ./pedelogo-catalogo/src/PedeLogo.Catalogo.Api/Dockerfile ./pedelogo-catalogo/
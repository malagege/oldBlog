---
title: .Net Core 建置 Docker 映像檔
date: 2021-06-27 15:02:16
tags: [docker,.net core]
categories: Docker
---

最近同事收到要把`.Net Core`程式轉移到 K8S，但是維運不會處理 Image，剛好我對 Docker 有小小的研究，但對 .Net Core 不是很熟，最後爬了文章，成功處理 Image。

<!--more-->

## Dockerfile 參考

[dotnet-docker/nuget-credentials.md at 203e3e7c2cbb6a4ac73290c603ef02d249128500 · dotnet/dotnet-docker · GitHub](https://github.com/dotnet/dotnet-docker/blob/203e3e7c2cbb6a4ac73290c603ef02d249128500/documentation/scenarios/nuget-credentials.md)

[dotnet-docker/samples/aspnetapp at main · dotnet/dotnet-docker · GitHub](https://github.com/dotnet/dotnet-docker/tree/main/samples/aspnetapp)

## 執行遇到錯誤

原開發是 window ，程式搬到 docker ，執行會有錯誤。如下:

```
#12 1.179 /usr/share/dotnet/sdk/5.0.202/Sdks/Microsoft.NET.Sdk/targets/Microsoft.PackageDependencyResolution.targets(241,5): error MSB4018: The "ResolvePackageAssets" task failed unexpectedly. [/source/xxxx/xxxx.csproj]
#12 1.179 /usr/share/dotnet/sdk/5.0.202/Sdks/Microsoft.NET.Sdk/targets/Microsoft.PackageDependencyResolution.targets(241,5): error MSB4018: NuGet.Packaging.Core.PackagingException: Unable to find fallback package folder 
```

由於 docker 上環境路徑不正確，做後發現下`dotnet restore`可以解決...，因為我不是寫 .Net Core 我就先忽略研究，初估重製環境設定路徑。

`dotnet restore`


## 範例

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
# COPY *.sln .
# COPY APP_PROJECT/*.csproj ./APP_PROJECT/


# copy everything else and build app
COPY APP_PROJECT/. ./APP_PROJECT/
WORKDIR /source/APP_PROJECT
RUN dotnet restore
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./

EXPOSE 80

ENTRYPOINT ["dotnet", "APP_PROJECT.dll"]
```

`APP_PROJECT` 再置換掉對應專案名稱



因為我朋友專案沒有sln 檔案，所以上面那幾但註解
這樣就順利 build 出 docker image
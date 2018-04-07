---
title: 使用Docker构建AspNetCore应用
date: 2018-01-26 20:47:57
tags: docker
---

``` bash
#Build Image Stage
FROM microsoft/aspnetcore-build:2 AS build-env
WORKDIR /api

# 以下为优化还原，因为项目文件不常变动Docker在构建过程可以使用Cache
COPY api.csproj .
RUN dotnet restore

COPY . .
RUN dotnet publish -o /publish

#Runtime Image Stage
FROM microsoft/aspnetcore:2
WORKDIR /publish
COPY --from=build-env /publish .
EXPOSE 80
ENTRYPOINT ["dotnet","api.dll"]
```

备注：api.dll是发布后的入口文件（项目名api)
建议和提醒：一般同学都使用Docker for Windows，Windows 10之前的版本只能使用Docker Toolbox,Docker 也只支持 Windows 10特定版本同时需要开启Hyper-V

建议使用Linux Container 这样的话官网镜像会小很多（Docker for Windows 分Linux Container 和Windows Container可以通过右击Docker图标弹出的上下文菜单切换），也可以是用Docker加速器
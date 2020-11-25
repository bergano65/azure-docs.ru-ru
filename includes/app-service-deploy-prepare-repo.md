---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004364"
---
## <a name="prepare-your-repository"></a>Подготовка репозитория

Чтобы получать автоматические сборки с сервера сборки Kudu Службы приложений Azure, убедитесь, что корень репозитория содержит нужные файлы проекта.

| Параметры выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _\*.SLN_-файлы, _\*.CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core | _\*.SLN_-файлы или _\*.CSPROJ_-файлы |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ или _package.json_ со сценарием запуска |
| Python | _\*PY_-файлы, _requirements.txt_ или _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| веб-задания; | _\<job_name>/run.\<extension>_ в папке _App\_Data/jobs/continuous_ для непрерывных веб-заданий или в папке _App\_Data/jobs/triggered_ для активируемых веб-заданий. См. сведения в [документации по веб-заданиям Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Функции | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Чтобы настроить развертывание, добавьте в корень репозитория *DEPLOYMENT*-файл. См. сведения о [настройке развертываний](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) и [настраиваемом скрипте развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Проект немедленно будет готов к развертыванию с помощью Git.
>


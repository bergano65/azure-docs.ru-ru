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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836780"
---
## <a name="prepare-your-repository"></a>Подготовка репозитория

Чтобы получить автоматические сборки с сервера сборки Azure App Service Kudu, убедитесь, что корень репозитория имеет правильные файлы в проекте.

| Среда выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _\*.SLN_-файлы, _\*.CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core | _\*.SLN_-файлы или _\*.CSPROJ_-файлы |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ или _package.json_ со сценарием запуска |
| Python | .py , _requirements.txt_или _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<job_name>/бег. расширение \<>_ под _App\_Data/jobs/continuous_ для непрерывного WebJobs, или _App\_Data/jobs/triggered_ для срабатывания WebJobs. Для получения дополнительной [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs)информации см. |
| Функции | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Чтобы настроить развертывание, добавьте в корень репозитория *DEPLOYMENT*-файл. Для получения дополнительной [информации](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) см. [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Проект немедленно готов к развертыванию с помощью Git.
>


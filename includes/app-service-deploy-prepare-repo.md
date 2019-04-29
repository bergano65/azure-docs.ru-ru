---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df987d1e13cb5330842fbab41dae96b24b581ddb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765621"
---
## <a name="prepare-your-repository"></a>Подготовка репозитория

Чтобы получить автоматическую сборку на сервере сборки Kudu для службы приложений Azure, убедитесь, что корень репозитория содержит нужные файлы в проекте.

| Среда выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _\*.SLN_-файлы, _\*.CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core; | _\*.SLN_-файлы или _\*.CSPROJ_-файлы |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ или _package.json_ со сценарием запуска |
| Python (только для Windows) | _\*PY_-файлы, _requirements.txt_ или _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<имя_задания>/run.\<extension>_ в папке _App\_Data/jobs/continuous_ (для непрерывных веб-заданий) или в папке _App\_Data/jobs/triggered_ (для активируемых веб-заданий). Дополнительные сведения см. в разделе [документации веб-заданиям Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Функции Azure | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Чтобы настроить развертывание, добавьте в корень репозитория _DEPLOYMENT_-файл. Дополнительные сведения см. в разделе [настраивать развертывания](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) и [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Проект немедленно готов к развертыванию с помощью Git.
>
>


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
ms.openlocfilehash: 7ec4028c319749b6a3da019e1d320d3937e9c4b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185760"
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
| Python | _\*PY_-файлы, _requirements.txt_ или _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<имя_задания>/run.\<extension>_ в папке _App\_Data/jobs/continuous_ (для непрерывных веб-заданий) или в папке _App\_Data/jobs/triggered_ (для активируемых веб-заданий). Дополнительные сведения см. в разделе [документации веб-заданиям Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Функции Azure | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Чтобы настроить развертывание, добавьте в корень репозитория _DEPLOYMENT_-файл. Дополнительные сведения см. в разделе [настраивать развертывания](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) и [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Проект немедленно готов к развертыванию с помощью Git.
>
>


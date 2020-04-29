---
title: Включить имя файла
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836780"
---
## <a name="prepare-your-repository"></a>Подготовка репозитория

Чтобы получать автоматические сборки из службы приложений Azure KUDU Build Server, убедитесь, что корневой каталог репозитория содержит нужные файлы в проекте.

| Параметры выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _\*.SLN_-файлы, _\*.CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core | _*. sln_ или _*. csproj_ |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _Server. js_, _app. js_или _Package. JSON_ с помощью сценария запуска |
| Python | . корректировка, _требования. txt_или _Runtime. txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<job_name>/рун.>\<расширения_ в разделе " _\_данные/задания_ " или "непрерывно" для непрерывных веб-заданий или " _данные\_приложения_ " или "задания", запускаемых для запущенных заданий. Дополнительные сведения см. в [документации по веб-заданиям KUDU](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Функции | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Чтобы настроить развертывание, добавьте в корень репозитория *DEPLOYMENT*-файл. Дополнительные сведения см. в статье [Настройка развертываний](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) и [пользовательского скрипта развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Проект сразу готов к развертыванию с помощью Git.
>


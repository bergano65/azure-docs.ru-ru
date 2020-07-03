---
title: Рабочие процессы GitHub Actions для Статических веб-приложений Azure
description: Сведения об использовании репозитория GitHub для настройки непрерывного развертывания в Статических веб-приложениях Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: 44472eb697a4d191d4ed99b7879654fcca61383b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655208"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Рабочие процессы GitHub Actions для предварительной версии Статических веб-приложений Azure

Azure создает рабочий процесс GitHub Actions для управления непрерывным развертыванием приложения при создании ресурса Статического веб-приложения Azure. Рабочий процесс управляется файлом YAML. В этой статье подробно описывается структура и параметры файла рабочего процесса.

Развертывания инициируются [триггерами](#triggers), которые выполняют [задания](#jobs), определяемые отдельными [шагами](#steps).

## <a name="file-location"></a>Размещение файла

При связывании репозитория GitHub со Статическими веб-приложениями Azure файл рабочего процесса добавляется в репозиторий.

Чтобы просмотреть созданный файл рабочего процесса, выполните следующие действия.

1. Откройте репозиторий приложения на сайте GitHub.
1. На вкладке _Code_ (Код) щелкните папку `.github/workflows`.
1. Щелкните файл с именем, которое выглядит как `azure-static-web-apps-<RANDOM_NAME>.yml`.

Файл YAML в репозитории будет выглядеть примерно так:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v1
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Триггеры

[Триггер](https://help.github.com/actions/reference/events-that-trigger-workflows) GitHub Actions сообщает рабочему процессу GitHub Actions, что нужно запустить задание на основе триггеров событий. Триггеры перечислены с помощью свойства `on` в файле рабочего процесса.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

С помощью параметров, связанных со свойством `on`, можно определить, какие ветви активируют задание, а также задать срабатывание триггеров для различных состояний запросов на вытягивание.

В этом примере рабочий процесс запускается при изменении _главной_ ветви. Изменения, запускающие рабочий процесс, включают в себя отправку фиксаций и открытие запросов на вытягивание для выбранной ветви.

## <a name="jobs"></a>Задания

Для каждого триггера события требуется обработчик событий. [Задания](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) определяют, что происходит при запуске события.

В файле рабочего процесса Статических веб-приложений есть два доступных задания.

| Имя  | Описание |
|---------|---------|
|`build_and_deploy_job` | Выполняется при отправке фиксаций или открытии запроса на вытягивание для ветви, указанной в свойстве `on`. |
|`close_pull_request_job` | Выполняется ТОЛЬКО при закрытии запроса на вытягивание. |

## <a name="steps"></a>Шаги

Шаги — это последовательные задачи задания. С помощью шага выполняются такие действия, как установка зависимостей, выполнение тестов и развертывание приложения в рабочей среде.

Файл рабочего процесса определяет следующие шаги.

| Задание  | Шаги  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Извлекает репозиторий в среде GitHub Actions.<li>Создает и развертывает репозиторий в Статических веб-приложениях Azure.</ol>|
| `close_pull_request_job` | <ol><li>Уведомляет Статические веб-приложения Azure о том, что запрос на вытягивание закрыт.</ol>|

## <a name="build-and-deploy"></a>Сборка и развертывание

Шаг с именем `Build and Deploy` выполняет сборку и развертывание в экземпляре Статических веб-приложений Azure. В разделе `with` можно настроить следующие значения для развертывания.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Свойство | Описание | Обязательно |
|---|---|---|
| `app_location` | Расположение кода приложения.<br><br>Например, введите `/`, если исходный код приложения находится в корне репозитория, или `/app`, если код приложения находится в каталоге с именем `app`. | Да |
| `api_location` | Расположение кода Функций Azure.<br><br>Например, введите `/api`, если код приложения находится в папке с именем `api`. Если в папке не обнаружено ни одного приложения Функций Azure, то в процессе сборки не произойдет сбой и в рабочем процессе предполагается, что API не нужен. | нет |
| `app_artifact_location` | Расположение выходного каталога сборки относительно `app_location`.<br><br>Например, если исходный код приложения находится в `/app`, а сценарий сборки выводит файлы в папку `/app/build`, установите `build` в качестве значения `app_artifact_location`. | нет |

Значения `repo_token`, `action` и `azure_static_web_apps_api_token`, настроенные с помощью Статических веб-приложений Azure, не должны изменяться вручную.

## <a name="custom-build-commands"></a>Команды настраиваемой сборки

Вы можете точно контролировать, какие команды выполняются во время развертывания. Следующие команды можно определить в разделе задания `with`.

Перед выполнением любой пользовательской команды для развертывания всегда вызывается `npm install`.

| Get-Help            | Описание |
|---------------------|-------------|
| `app_build_command` | Определяет пользовательскую команду, выполняемую во время развертывания приложения статического содержимого.<br><br>Например, чтобы настроить производственную сборку для приложения Angular, введите `ng build --prod`. Если оставить это поле пустым, рабочий процесс попытается выполнить команды `npm run build` или `npm run build:Azure`.  |
| `api_build_command` | Определяет пользовательскую команду, выполняемую во время развертывания приложения API Функций Azure. |

## <a name="route-file-location"></a>Расположение файла маршрута

Рабочий процесс можно настроить для поиска файла [routes.json](routes.md) в любой папке в репозитории. Следующие свойства можно определить в разделе задания `with`.

| Свойство            | Описание |
|---------------------|-------------|
| `routes_location` | Определяет расположение каталога, в котором найден файл _routes.json_. Это расположение задается относительно корня репозитория. |

 Явное расположение файла _routes.json_ особенно важно, если шаг сборки внешней платформы не перемещает этот файл в `app_artifact_location` по умолчанию.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Проверка запросов на вытягивание в подготовительных средах](review-publish-pull-requests.md)

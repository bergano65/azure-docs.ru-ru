---
title: Настройка приложений Ruby — Служба приложений Azure
description: В этом руководстве описаны возможности создания и настройки приложений Ruby для Службы приложений Azure под управлением Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 8a2eaf50a35b25463be3e323d4362b52e2339bf6
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550294"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Настройка приложения Ruby в Linux для Службы приложений Azure

В этой статье объясняется, как [Служба приложений Azure](app-service-linux-intro.md) запускает приложения Ruby, а также как вы можете настроить поведение Службы приложений при необходимости. Приложения Ruby нужно развертывать со всеми необходимыми модулями [pip](https://pypi.org/project/pip/).

Это руководство содержит основные понятия и инструкции для разработчиков Ruby, которые используют встроенный контейнер Linux в Службе приложений. Если вы раньше не использовали Службу приложений Azure, сначала ознакомьтесь с [кратким руководством по Ruby](quickstart-ruby.md) и [руководством по использованию Ruby с PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Отображение сведений о версии Ruby

Чтобы отобразились сведения о текущей версии Ruby, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразились сведения обо всех поддерживаемых версиях Ruby, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Вместо них вы можете запустить любую неподдерживаемую версию Ruby, скомпилировав собственный образ контейнера. Дополнительные сведения см. в статье об [использовании пользовательского образа Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Настройка версии Ruby

Выполните следующую команду в [Cloud Shell](https://shell.azure.com), чтобы задать для Ruby версию 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Во время развертывания вы можете увидеть ошибки следующего вида:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> или
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Это означает, что настроенная в проекте версия Ruby отличается от версии, которая установлена в выполняемом контейнере (в приведенном выше примере это `2.3.3`). В приведенном выше примере просмотрите *Gemfile* и *.ruby-version* и убедитесь, что версия Ruby не указана или указана та же версия, что и в используемом контейнере (в приведенном выше примере это `2.3.3`).

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы сможете обращаться к ним, используя стандартный шаблон [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html). Например, для доступа к параметру приложения с именем `WEBSITE_SITE_NAME` используйте следующий код:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Настройка развертывания

Если вы развертываете [репозиторий Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) или [ZIP-пакет](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) с включенными процессами сборки, механизм развертывания (Kudu) по умолчанию автоматически выполняет следующие шаги после развертывания:

1. Проверка наличия *Gemfile*.
1. Запустите `bundle clean`. 
1. Запустите `bundle install --path "vendor/bundle"`.
1. Выполнение `bundle package` для упаковки пакетов в папку vendor/cache.

### <a name="use---without-flag"></a>Использование флага --without

Чтобы выполнить `bundle install` с флагом [--without](https://bundler.io/man/bundle-install.1.html), сохраните в [параметр приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `BUNDLE_WITHOUT` список групп с разделителями запятыми. Например, приведенная ниже команда позволяет присвоить ему значение `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Если этот параметр определен, механизм развертывания выполняет `bundle install` с `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Предварительная компиляция ресурсов

По умолчанию предварительная компиляция ресурсов не входит в список действий после развертывания. Чтобы включить предварительную компиляцию активов, присвойте [параметру приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `ASSETS_PRECOMPILE` значение `true`. Теперь команда `bundle exec rake --trace assets:precompile` будет выполняться последней в списке действий после развертывания. Например:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Дополнительную информацию см. в статье, посвященной [предоставлению статических ресурсов](#serve-static-assets).

## <a name="customize-start-up"></a>Настройка запуска

По умолчанию контейнер Ruby запускает сервер Rails в следующей последовательности (дополнительные сведения см. в [описании скрипта запуска](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Создание значения [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), если его еще нет. Это значение требуется для запуска приложения в рабочем режиме.
1. Присвоение переменной среды `RAILS_ENV` значения `production`.
1. Удаление всех файлов *.pid* в каталоге *tmp/pids*, которые остались от ранее запущенного сервера Rails.
1. Проверка установки всех зависимостей. Если чего-то не хватает, следует установить пакеты из локального каталога *vendor/cache*.
1. Запустите `rails server -e $RAILS_ENV`.

Процесс загрузки можно настроить следующими способами:

- [предоставление статических ресурсов](#serve-static-assets);
- [выполнение в режиме непроизводственной среды](#run-in-non-production-mode);
- [настройка secret_key_base вручную](#set-secret_key_base-manually).

### <a name="serve-static-assets"></a>Предоставление статических ресурсов

Сервер Rails в контейнере Ruby по умолчанию работает в режиме производственной среды и [ожидает, что все ресурсы заранее скомпилированы и предоставляются веб-сервером](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Чтобы предоставлять статические ресурсы из сервера Rails, следует обеспечить следующее:

- **Предварительная компиляция ресурсов** - [предварительно скомпилируйте статические ресурсы локально](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) и вручную разверните их. Также вы можете поручить этот процесс механизму развертывания (подробнее см. статью [о предварительной компиляции ресурсов](#precompile-assets)).
- **Настройка предоставления статических файлов** — чтобы предоставлять статические ресурсы из контейнера Ruby, `RAILS_SERVE_STATIC_FILES` [присвойте параметру приложения `RAILS_SERVE_STATIC_FILES`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) значение `true`. Например:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Выполнение в режиме непроизводственной среды

По умолчанию сервер Rails выполняется в режиме производственной среды. Чтобы переключить его, например, в режим разработки, задайте [параметру приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `RAILS_ENV` значение `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Но этот параметр сам по себе только запускает сервер Rails в режиме разработки. То есть он будет обрабатывать запросы только от localhost и не будет доступен вне контейнера. Чтобы принимать запросы от удаленных клиентов, задайте [параметру приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `APP_COMMAND_LINE` значение `rails server -b 0.0.0.0`. Этот параметр приложения позволяет выполнить в контейнере Ruby пользовательскую команду. Например:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually">настройка secret_key_base вручную</a>.

Чтобы использовать собственное значение `secret_key_base` вместо автоматически созданного службой приложений, присвойте желаемое значение [параметру приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `SECRET_KEY_BASE`. Например:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Приложение Ruby с PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)

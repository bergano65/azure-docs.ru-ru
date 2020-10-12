---
title: Использование GitHub Actions для развертывания статического сайта в службе хранилища Azure
description: Размещение статического веб-сайта службы хранилища Azure с помощью действий GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 919fa0d7b6dff0361e4439b442bcfe9648ed8677
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776397"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Настройка рабочего процесса GitHub Actions для развертывания статического веб-сайта в службе хранилища Azure

Начало работы с [действиями GitHub](https://docs.github.com/en/actions) с помощью рабочего процесса для развертывания статического сайта в хранилище BLOB-объектов Azure. После настройки рабочего процесса для действий GitHub вы сможете автоматически развернуть сайт в Azure из GitHub при внесении изменений в код сайта. 

> [!NOTE]
> Если вы используете [статические веб-приложения Azure](https://docs.microsoft.com/azure/static-web-apps/), вам не нужно вручную настраивать рабочий процесс для действий GitHub.
> Статические веб-приложения Azure автоматически создают рабочий процесс GitHub. 

## <a name="prerequisites"></a>Предварительные требования

Подписка Azure и учетная запись GitHub. 

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Учетная запись GitHub со статическим кодом веб-сайта. Если у вас нет учетной записи GitHub, [Зарегистрируйтесь бесплатно](https://github.com/join).  
- Рабочий статический веб-сайт, размещенный в службе хранилища Azure. Сведения о [размещении статического веб-сайта в службе хранилища Azure](storage-blob-static-website-how-to.md). Статический веб-сайт должен включать [Azure CDN](static-website-content-delivery-network.md).

## <a name="generate-deployment-credentials"></a>Создать учетные данные развертывания

Вы можете создать [субъект-службу](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) в [Azure CLI](/cli/azure/). Выполните эту команду с [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

Замените заполнитель `myStaticSite` именем сайта, размещенным в службе хранилища Azure. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

В приведенном выше примере Замените заполнители ИДЕНТИФИКАТОРом подписки и именем группы ресурсов. Выходные данные — это объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению службы приложений, как показано ниже. Скопируйте этот объект JSON для последующего использования.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Область в предыдущем примере ограничена конкретным приложением службы приложений, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

1. В [GitHub](https://github.com/)найдите свой репозиторий.

1. Выберите **параметры > секреты > новый секрет**.

1. Вставьте все выходные данные JSON из команды Azure CLI в поле значения секрета. Присвойте секрету имя, например `AZURE_CREDENTIALS` .

    Когда вы настроите файл рабочего процесса позже, используйте секрет для входа `creds` в действие Azure Login. Пример:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Добавление рабочего процесса

1. Перейдите к **действиям** для репозитория GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Пункт меню действий GitHub&quot;:::

1. Выберите **настроить рабочий процесс самостоятельно**. 

1. Удалите все после `on:` раздела файла рабочего процесса. Например, оставшийся рабочий процесс может выглядеть следующим образом. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Переименуйте рабочий процесс `Blob storage website CI` и добавьте действия по извлечению и входу. Эти действия приводят к извлечению кода сайта и проверке подлинности в Azure с помощью `AZURE_CREDENTIALS` созданного ранее секрета GitHub. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Используйте действие Azure CLI, чтобы передать код в хранилище BLOB-объектов и очистить конечную точку CDN. Для `az storage blob upload-batch` Замените заполнитель именем своей учетной записи хранения. Скрипт будет отправлен в `$web` контейнер. Для `az cdn endpoint purge` Замените заполнители именем профиля CDN, именем конечной точки CDN и группой ресурсов.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Завершите рабочий процесс, добавив действие для выхода из Azure. Ниже приведен Завершенный рабочий процесс. Файл появится в `.github/workflows` папке репозитория.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Проверка развертывания

1. Перейдите к **действиям** для репозитория GitHub. 

1. Откройте первый результат, чтобы просмотреть подробные журналы выполнения рабочего процесса. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Пункт меню действий GitHub&quot;:::

1. Выберите **настроить рабочий процесс самостоятельно**. 

1. Удалите все после `on:` раздела файла рабочего процесса. Например, оставшийся рабочий процесс может выглядеть следующим образом. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Переименуйте рабочий процесс `Blob storage website CI` и добавьте действия по извлечению и входу. Эти действия приводят к извлечению кода сайта и проверке подлинности в Azure с помощью `AZURE_CREDENTIALS` созданного ранее секрета GitHub. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Используйте действие Azure CLI, чтобы передать код в хранилище BLOB-объектов и очистить конечную точку CDN. Для `az storage blob upload-batch` Замените заполнитель именем своей учетной записи хранения. Скрипт будет отправлен в `$web` контейнер. Для `az cdn endpoint purge` Замените заполнители именем профиля CDN, именем конечной точки CDN и группой ресурсов.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Завершите рабочий процесс, добавив действие для выхода из Azure. Ниже приведен Завершенный рабочий процесс. Файл появится в `.github/workflows` папке репозитория.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>Очистка ресурсов

Если статический сайт и репозиторий Azure больше не нужны, очистите развернутые ресурсы, удалив группу ресурсов и репозиторий GitHub. 

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Дополнительные сведения о статических веб-приложениях Azure](https://docs.microsoft.com/azure/static-web-apps/)
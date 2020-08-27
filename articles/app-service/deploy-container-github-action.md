---
title: Настраиваемые контейнеры CI/CD из действий GitHub
description: Узнайте, как использовать действия GitHub для развертывания пользовательского контейнера Linux в службе приложений из конвейера CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 6af23aba28ce3cda9982878ed08ec515aa25633a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962610"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Развертывание пользовательского контейнера в службе приложений с помощью действий GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) предоставляет гибкие возможности для создания автоматизированных рабочих процессов жизненного цикла разработки программного обеспечения. [Действие службы приложений Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)позволяет автоматизировать рабочий процесс для развертывания [службы приложений](overview.md) настраиваемых контейнеров с помощью действий GitHub.

> [!IMPORTANT]
> GitHub Actions в настоящее время предоставляется в виде бета-версии. Сначала вам необходимо [зарегистрироваться для присоединения к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса.

Для рабочего процесса контейнера службы приложений Azure файл содержит три раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определите субъект-службу. <br /> 2. Создайте секрет GitHub. |
|**Сборка** | 1. Настройте среду. <br /> 2. Создайте образ контейнера. |
|**Развертывание** | 1. Разверните образ контейнера. |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](/cli/azure/). Эту команду можно выполнить в [Azure Cloud Shell](https://shell.azure.com/) на портале Azure или с помощью кнопки **Попробовать**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
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
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Вы можете ограничить область в приведенной выше команде AZ CLI конкретным приложением службы приложений и реестром контейнеров Azure, в который отправляются образы контейнеров.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Вставьте содержимое выходных данных JSON из поля [Создание субъекта-службы](#create-a-service-principal) в качестве значения переменной Secret. Присвойте секрету имя, например `AZURE_CREDENTIALS` .

Когда вы настроите файл рабочего процесса позже, используйте секрет для входа `creds` в действие Azure Login. Пример:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

Аналогичным образом определите следующие дополнительные секреты для учетных данных реестра контейнеров и настройте их в действии входа DOCKER.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>Создание образа контейнера

В следующем примере показана часть рабочего процесса, который создает образ DOCKER.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Развертывание в контейнер службы приложений

Чтобы развернуть образ в пользовательском контейнере в службе приложений, используйте `azure/webapps-container-deploy@v1` действие. Это действие имеет пять параметров:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **app-name** | (Обязательный) Имя приложения Службы приложений. | 
| **slot-name** | (Необязательный) Введите существующий слот вместо рабочего. |
| **images** | Необходимости Укажите полное имя образа контейнера. Например, "myregistry.azurecr.io/nginx:latest" или "Python: 3.7.2-Alpine/". Для многоконтейнерного приложения можно указать несколько имен образов контейнеров (разделенных несколькими строками). |
| **файл конфигурации** | Используемых Путь к файлу создания DOCKER. Должен быть полным путем или относительным по отношению к рабочему каталогу по умолчанию. Требуется для приложений с несколькими контейнерами. |
| **контейнер — команда** | Используемых Введите команду запуска. Для ex Запуск DotNet или DotNet filename.dll |

Ниже приведен пример рабочего процесса для сборки и развертывания Node.js приложения в пользовательском контейнере в службе приложений. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти наш набор компонентов Actions, объединенных в разных репозитории, а также соответствующую документацию и примеры использования GitHub для CI/CD и развертыванию приложений в Azure на GitHub.

- [Рабочие процессы действий для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)

- [Веб-приложение Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в Docker и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)
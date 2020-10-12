---
title: Настраиваемые контейнеры CI/CD из действий GitHub
description: Узнайте, как использовать действия GitHub для развертывания пользовательского контейнера Linux в службе приложений из конвейера CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 3a5e319115c124551c05f2ac5aa393ba19596d0d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893362"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Развертывание пользовательского контейнера в службе приложений с помощью действий GitHub

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) дают возможность создать автоматизированный рабочий процесс разработки программного обеспечения. С помощью [действия веб-развертывание Azure](https://github.com/Azure/webapps-deploy)можно автоматизировать рабочий процесс, чтобы развернуть пользовательские контейнеры в [службе приложений](overview.md) , используя действия GitHub.

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит различные шаги и параметры, которые находятся в рабочем процессе.

Для рабочего процесса контейнера службы приложений Azure файл содержит три раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. получение субъекта-службы или профиля публикации. <br /> 2. Создайте секрет GitHub. |
|**Сборка** | 1. Создайте среду. <br /> 2. Создайте образ контейнера. |
|**Развертывание** | 1. Разверните образ контейнера. |

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  
- Рабочий реестр контейнеров и приложение службы приложений Azure для контейнеров. В этом примере используется реестр контейнеров Azure. 
    - [Узнайте, как создать контейнерное Node.jsное приложение с помощью DOCKER, отправить образ контейнера в реестр, а затем развернуть образ в службе приложений Azure.](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Создать учетные данные развертывания

Рекомендуемый способ проверки подлинности в службе приложений Azure для действий GitHub — профиль публикации. Вы также можете пройти проверку подлинности с помощью субъекта-службы, но процесс требует дополнительных действий. 

Сохраните учетные данные профиля публикации или субъекта-службы в качестве [секрета GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) для аутентификации в Azure. Вы получите доступ к секрету в рабочем процессе. 

# <a name="publish-profile"></a>[Опубликовать профиль](#tab/publish-profile)

Профиль публикации — это учетные данные на уровне приложения. Настройте профиль публикации в качестве секрета GitHub. 

1. Перейдите к службе приложений в портал Azure. 

1. На странице **Обзор** выберите **получить профиль публикации**.

1. Сохраните скачанный файл. Вы будете использовать содержимое файла для создания секрета GitHub.

# <a name="service-principal"></a>[Субъект-служба](#tab/service-principal)

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) в [Azure CLI](/cli/azure/). Выполните эту команду с [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

В примере Замените заполнители ИДЕНТИФИКАТОРом подписки, именем группы ресурсов и именем приложения. Выходные данные — это объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению службы приложений. Скопируйте этот объект JSON для последующего использования.

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

---

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Вставьте содержимое выходных данных JSON в качестве значения переменной Secret. Присвойте секрету имя, например `AZURE_CREDENTIALS` .

Когда вы настроите файл рабочего процесса позже, используйте секрет для входа `creds` в действие Azure Login. Пример:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Настройка секрета GitHub для проверки подлинности

# <a name="publish-profile"></a>[Опубликовать профиль](#tab/publish-profile)

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Чтобы использовать [учетные данные уровня приложения](#generate-deployment-credentials), вставьте содержимое скачанного файла профиля публикации в поле значение секрета. Назовите секрет `AZURE_WEBAPP_PUBLISH_PROFILE` .

При настройке рабочего процесса GitHub используйте `AZURE_WEBAPP_PUBLISH_PROFILE` в действии развертывание веб-приложения Azure. Пример:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Субъект-служба](#tab/service-principal)

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Чтобы использовать [учетные данные на уровне пользователя](#generate-deployment-credentials), вставьте все выходные данные JSON из команды Azure CLI в поле значения секрета. Присвойте секрету имя, например `AZURE_CREDENTIALS` .

Когда вы настроите файл рабочего процесса позже, используйте секрет для входа `creds` в действие Azure Login. Пример:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Настройка секретов GitHub для реестра

Определите секреты для использования с действием DOCKER Login. 

1. Перейдите к контейнеру в портал Azure или DOCKER и скопируйте имя пользователя и пароль. 

2. Определите новый секрет для имени пользователя реестра с именем `REGISTRY_USERNAME` . 

3. Определите новый секрет для пароля реестра с именем `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Создание образа контейнера

В следующем примере показана часть рабочего процесса, который создает Node.JS образ DOCKER. Используйте [имя входа DOCKER](https://github.com/azure/docker-login) для входа в закрытый реестр контейнеров. В этом примере используется реестр контейнеров Azure, но одно и то же действие работает для других реестров. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Можно также использовать [имя входа DOCKER](https://github.com/azure/docker-login) для одновременного входа в несколько реестров контейнеров. Этот пример включает два новых секрета GitHub для проверки подлинности с помощью docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Развертывание в контейнер службы приложений

Чтобы развернуть образ в пользовательском контейнере в службе приложений, используйте `azure/webapps-deploy@v2` действие. Это действие имеет пять параметров:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **app-name** | (Обязательный) Имя приложения Службы приложений. | 
| **publish-profile** | (Необязательный) Содержимое файла профиля публикации с секретами для веб-развертывания. |
| **images** | Полное имя образа контейнера. Например, "myregistry.azurecr.io/nginx:latest" или "Python: 3.7.2-Alpine/". Для сценария с несколькими контейнерами можно указать несколько имен образов контейнеров (с несколькими строками). |
| **slot-name** | (Необязательный) Введите существующий слот вместо рабочего. |
| **файл конфигурации** | Используемых Путь к файлу Docker-Compose |

# <a name="publish-profile"></a>[Опубликовать профиль](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Субъект-служба](#tab/service-principal)

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
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти наш набор компонентов Actions, объединенных в разных репозитории, а также соответствующую документацию и примеры использования GitHub для CI/CD и развертыванию приложений в Azure на GitHub.

- [Рабочие процессы действий для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)

- [Вход в Docker и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)
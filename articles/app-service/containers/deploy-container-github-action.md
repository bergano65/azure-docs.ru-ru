---
title: Развертывание контейнера из конвейера CI/CD с помощью действий GitHub в службе приложений Azure | Документация Майкрософт
description: Узнайте, как использовать действия GitHub для развертывания контейнера в службе приложений.
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809816"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>Действия GitHub для развертывания в Веб-приложение для контейнеров

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) дают возможность создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. Действия службы приложений Azure для GitHub позволяют автоматизировать рабочий процесс для развертывания [веб-приложений Azure для контейнеров](https://azure.microsoft.com/services/app-service/containers/) с помощью действий GitHub.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Сначала необходимо [зарегистрироваться, чтобы присоединиться к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, составляющие рабочий процесс.

Для рабочего процесса контейнера веб-приложения Azure файл содержит три раздела:

|Section  |Задачи  |
|---------|---------|
|**Authentication** (Аутентификация) | 1. Определение субъекта-службы <br /> 2. Создание секрета GitHub |
|**Сборка** | 1. Настройка среды <br /> 2. Создание образа контейнера |
|**Развертывание** | 1. Развертывание образа контейнера |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](https://docs.microsoft.com/cli/azure/). Эту команду можно выполнить с помощью [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

В этом примере Замените заполнители в ресурсе ИДЕНТИФИКАТОРом подписки, группой ресурсов и именем веб-приложения. Выходные данные — это учетные данные назначения роли, которые обеспечивают доступ к веб-приложению. Скопируйте этот объект JSON, который можно использовать для проверки подлинности из GitHub.

> [!NOTE]
> Не нужно создавать субъект-службу, если вы решили использовать профиль публикации для проверки подлинности.

> [!IMPORTANT]
> Всегда рекомендуется предоставлять минимальный доступ. Именно поэтому область в предыдущем примере ограничена конкретным веб-приложением, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

В приведенном ниже примере используются учетные данные уровня пользователя, т. е. субъект-служба Azure для развертывания. Выполните действия по настройке секрета.

1. В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > Добавить новый секрет**

2. Вставьте содержимое приведенной ниже `az cli` команды в качестве значения переменной Secret. Пример: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Теперь в файле рабочего процесса в ветви: `.github/workflows/workflow.yml` замените секрет в действии Azure login своим секретом.

4. Аналогичным образом определите следующие дополнительные секреты для учетных данных реестра контейнеров и настройте их в действии входа DOCKER. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Вы увидите секреты, как показано ниже, после определения.

    ![Секреты контейнеров](../media/app-service-github-actions/app-service-secrets-container.png)

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
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Развертывание в контейнер веб-приложения

Чтобы развернуть образ в контейнере веб-приложения, необходимо использовать действие `Azure/appservice-actions/webapp@master`. Это действие имеет 5 параметров:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **имя приложения** | Необходимости Имя веб-приложения Azure | 
| **имя слота** | Используемых Введите существующий слот, отличный от рабочего слота |
| **images** | Необходимости Укажите полное имя образа контейнера. Например, "myregistry.azurecr.io/nginx:latest" или "Python: 3.7.2-Alpine/". Для сценария с несколькими контейнерами можно указать несколько имен образов контейнеров (с несколькими строками). |
| **файл конфигурации** | Используемых Путь к файлу создания DOCKER. Должен быть полным путем или относительным по отношению к рабочему каталогу по умолчанию. Требуется для сценария с несколькими контейнерами |
| **контейнер — команда** | Используемых Введите команду запуска. Для ex Команда DotNet Run или DotNet filename. dll |

Ниже приведен пример рабочего процесса для создания и развертывания веб-приложения Node. js в контейнере веб-приложения Azure.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

Наш набор действий, сгруппированных в различные репозитории в GitHub, можно найти в каждом из них, содержащих документацию и примеры, которые помогут вам использовать GitHub для непрерывной интеграции и развертывания приложений в Azure.

- [Вход в Azure](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в DOCKER и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание K8s](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)

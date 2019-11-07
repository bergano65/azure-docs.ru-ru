---
title: Развертывание контейнера из конвейера CI/CD с помощью действий GitHub в службе приложений Azure | Документация Майкрософт
description: Узнайте, как использовать действия GitHub для развертывания контейнера в службе приложений.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7fbd7b571f5590ff35d52062cc621069a47b619c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620234"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Развертывание пользовательского контейнера в службе приложений с помощью действий GitHub

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) дают возможность создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. [Действие службы приложений Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)позволяет автоматизировать рабочий процесс для развертывания приложений в качестве [пользовательских контейнеров в службе приложений](https://azure.microsoft.com/services/app-service/containers/) с помощью действий GitHub.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Сначала необходимо [зарегистрироваться, чтобы присоединиться к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (yml) в пути `/.github/workflows/` в репозитории. Это определение содержит различные шаги и параметры, составляющие рабочий процесс.

Для рабочего процесса контейнера службы приложений Azure файл содержит три раздела:

|Раздел  |Задачи  |
|---------|---------|
|**Проверка подлинности** | 1. Определите субъект-службу. <br /> 2. Создайте секрет GitHub. |
|**Сборка** | 1. Настройте среду. <br /> 2. Создайте образ контейнера. |
|**Развертывание** | 1. Разверните образ контейнера. |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](https://docs.microsoft.com/cli/azure/). Эту команду можно выполнить с помощью [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Выходные данные — это объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению службы приложений, как показано ниже. Скопируйте этот объект JSON для проверки подлинности из GitHub.

 ```azurecli 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Всегда рекомендуется предоставлять минимальный доступ. Вы можете ограничить область в приведенной выше команде AZ CLI конкретным приложением службы приложений и реестром контейнеров Azure, в который отправляются образы контейнеров.

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

Чтобы развернуть образ в пользовательском контейнере в службе приложений, используйте действие `azure/webapps-container-deploy@v1`. Это действие имеет пять параметров:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **имя приложения** | Необходимости Имя приложения службы приложений | 
| **имя слота** | Используемых Введите существующий слот, отличный от рабочего слота |
| **images** | Необходимости Укажите полное имя образа контейнера. Например, "myregistry.azurecr.io/nginx:latest" или "Python: 3.7.2-Alpine/". Для многоконтейнерного приложения можно указать несколько имен образов контейнеров (разделенных несколькими строками). |
| **файл конфигурации** | Используемых Путь к файлу создания DOCKER. Должен быть полным путем или относительным по отношению к рабочему каталогу по умолчанию. Требуется для приложений с несколькими контейнерами. |
| **контейнер — команда** | Используемых Введите команду запуска. Для ex Команда DotNet Run или DotNet filename. dll |

Ниже приведен пример рабочего процесса для сборки и развертывания приложения Node. js в пользовательском контейнере в службе приложений.

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

Наш набор действий, сгруппированных в различные репозитории в GitHub, можно найти в каждом из них, содержащих документацию и примеры, которые помогут вам использовать GitHub для непрерывной интеграции и развертывания приложений в Azure.

- [Вход в Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в DOCKER и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание K8s](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы CI](https://github.com/actions/starter-workflows)

- [Начальные рабочие процессы для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

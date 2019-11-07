---
title: Создание, тестирование и развертывание контейнеров в службе Kubernetes Azure с помощью действий GitHub
description: Узнайте, как использовать действия GitHub для развертывания контейнера в Kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: c82cbb8d7406c8d6f4063f52590e050438283ace
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693388"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Действия GitHub для развертывания в службе Kubernetes

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) дают возможность создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. Действие Kubernetes [azure/aks-set-context@v1]((https://github.com/Azure/aks-set-context)) упрощает развертывание кластеров службы Kubernetes Azure. Действие задает контекст целевого кластера AKS, который может использоваться другими действиями, такими как [Azure/K8S-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8S-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) и т. д., или выполнение любых команд kubectl.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Сначала необходимо [зарегистрироваться, чтобы присоединиться к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (yml) в пути `/.github/workflows/` в репозитории. Это определение содержит различные шаги и параметры, составляющие рабочий процесс.

Для рабочего процесса, предназначенного для AKS, файл содержит три раздела:

|Раздел  |Задачи  |
|---------|---------|
|**Проверка подлинности** | Вход в частный реестр контейнеров (запись контроля доступа) |
|**Сборка** | Сборка & принудительная отправка образа контейнера  |
|**Развертывание** | 1. Настройка целевого кластера AKS |
| |2. Создание секрета Generic/DOCKER-Registry в кластере Kubernetes  |
||3. Развертывание в кластере Kubernetes|

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](https://docs.microsoft.com/cli/azure/). Эту команду можно выполнить с помощью [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

В приведенной выше команде замените заполнители ИДЕНТИФИКАТОРом подписки и группой ресурсов. Выходные данные — это учетные данные назначения роли, которые обеспечивают доступ к ресурсу. Команда должна вывести объект JSON, аналогичный этому.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Скопируйте этот объект JSON, который можно использовать для проверки подлинности из GitHub.

## <a name="configure-the-github-secrets"></a>Настройка секретов GitHub

Выполните действия по настройке секретов:

1. В [GitHub](https://github.com/)перейдите к репозиторию, выберите **параметры > секреты > Добавить новый секрет**.

    ![секретные коды](media/kubernetes-action/secrets.png)

2. Вставьте содержимое указанной выше `az cli` команды в качестве значения переменной Secret. Пример: `AZURE_CREDENTIALS`.

3. Аналогичным образом определите следующие дополнительные секреты для учетных данных реестра контейнеров и настройте их в действии входа DOCKER. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Вы увидите секреты, как показано ниже, после определения.

    ![kubernetes — секреты](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Создание образа контейнера и развертывание в кластере службы Kubernetes Azure

Сборка и отправка образов контейнеров выполняются с помощью `Azure/docker-login@v1` действия. Чтобы развернуть образ контейнера в AKS, необходимо использовать действие `Azure/k8s-deploy@v1`. Это действие имеет пять параметров:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **namespace** | Используемых Выберите целевое пространство имен Kubernetes. Если пространство имен не указано, команды будут выполняться в пространстве имен по умолчанию. | 
| **манифесты** |  Необходимости Путь к файлам манифеста, который будет использоваться для развертывания |
| **images** | Используемых Полный URL-адрес ресурса для образов, используемых для подстановок в файлах манифеста |
| **имажепуллсекретс** | Используемых Имя секрета DOCKER-Registry, который уже был настроен в кластере. Каждое из этих имен секретов добавляется в поле Имажепуллсекретс для рабочих нагрузок, найденных во входных файлах манифеста. |
| **kubectl — версия** | Используемых Устанавливает определенную версию двоичного файла kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Развертывание в кластере службы Kubernetes Azure

Сквозной рабочий процесс для создания образов контейнеров и развертывания в кластере службы Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Дальнейшие действия

Наш набор действий можно найти в разных репозиториях на сайте GitHub, на каждом из которых содержится документация и примеры, которые помогут вам использовать GitHub для непрерывной интеграции и развертывания приложений в Azure.

- [Программа установки — kubectl](https://github.com/Azure/setup-kubectl)

- [K8S-Set-context](https://github.com/Azure/k8s-set-context)

- [AKS-Set-context](https://github.com/Azure/aks-set-context)

- [K8S — создание секрета](https://github.com/Azure/k8s-create-secret)

- [K8S — развертывание](https://github.com/Azure/k8s-deploy)

- [веб-приложения — контейнер — развертывание](https://github.com/Azure/webapps-container-deploy)

- [действия — рабочие процессы — примеры](https://github.com/Azure/actions-workflow-samples)

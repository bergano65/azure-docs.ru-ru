---
title: Сборка, тестирование и развертывание контейнеров в службе Azure Kubernetes с помощью действий GitHub
description: Узнайте, как использовать действия GitHub для развертывания контейнера в Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595371"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Действия GitHub для развертывания в сервисе Kubernetes

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) позволяют вам гибко создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. Действия [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Kubernetes облегчают развертывание кластеров служб Azure Kubernetes. В действии устанавливается контекст кластера akS, который может быть использован другими действиями, такими как [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) и т.д., или запускать любые команды kubectl.

Рабочий процесс определяется файлом YAML (.yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, которые составляют рабочий процесс.

Для рабочего процесса, ориентированного на AKS, файл имеет три раздела:

|Section  |Задания  |
|---------|---------|
|**Проверка подлинности** | Войти в реестр частных контейнеров (ACR) |
|**Построить** | Сборка & нажатия изображения контейнера  |
|**Развернуть** | 1. Установить целевой кластер AKS |
| |2. Создание общего/докер-реестра секрет в кластере Kubernetes  |
||3. Развертывание в кластере Кубернете|

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [основную службу,](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) используя команду [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI.](https://docs.microsoft.com/cli/azure/) Вы можете запустить эту команду с помощью [оболочки Облака Azure](https://shell.azure.com/) на портале Azure или выбрав кнопку **«Попробуй теску».**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

В приведенной выше команде замените заполнителей идентификатором подписки и группой ресурсов. Вывод — это учетные данные назначения ролей, которые обеспечивают доступ к вашему ресурсу. Команда должна вывести объект JSON, подобный этому.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Скопируйте этот объект JSON, который можно использовать для проверки подлинности от GitHub.

## <a name="configure-the-github-secrets"></a>Настройка секретов GitHub

Выполните действия по настройке секретов:

1. В [GitHub,](https://github.com/)просматривать репозиторий, выберите **Настройки > Секреты > Добавить новый секрет.**

    ![секретные коды](media/kubernetes-action/secrets.png)

2. Вставьте содержимое вышеуказанной `az cli` команды как значение секретной переменной. Например, `AZURE_CREDENTIALS`.

3. Аналогичным образом, определите следующие дополнительные секреты для учетных данных реестра контейнеров и установите их в действие Docker login. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Вы увидите секреты, как показано ниже, когда-то определено.

    ![kubernetes-секреты](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Создайте изображение контейнера и развернуть в кластере службы Azure Kubernetes

Сборка и нажатие изображений `Azure/docker-login@v1` контейнера осуществляется с помощью действий. Для развертывания изображения контейнера в AKS необходимо `Azure/k8s-deploy@v1` использовать действие. Это действие имеет пять параметров:

| **Параметр**  | **Объяснение**  |
|---------|---------|
| **namespace** | (Необязательно) Выберите целевое пространство имен Kubernetes. Если пространство имен не предусмотрено, команды будут работать в пространстве имен по умолчанию | 
| **Манифесты** |  (Обязательно) Путь к файлам манифеста, которые будут использоваться для развертывания |
| **Изображения** | (Необязательно) Полностью квалифицированный URL-адрес ресурса изображения (ы), который будет использоваться для замен в файлах манифеста |
| **imagepullsecrets** | (Необязательно) Название секрета докер-реестра, который уже создан внутри кластера. Каждое из этих секретных имен добавляется под полем imagePullSecrets для рабочих нагрузок, найденных в файлах входному манифеста |
| **kubectl-версия** | (Необязательно) Устанавливает конкретную версию двоичного kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Развертывание в сервисный кластер Azure Kubernetes

От окончания до конца рабочего процесса для создания изображений контейнеров и развертывания в кластере службы Azure Kubernetes.

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

Вы можете найти наш набор действий в различных репозиториях на GitHub, каждый из которых содержит документацию и примеры, которые помогут вам использовать GitHub для CI/CD и развернуть свои приложения в Azure.

- [установка-кубектль](https://github.com/Azure/setup-kubectl)

- [k8s-сет-контекст](https://github.com/Azure/k8s-set-context)

- [aks-set-контекст](https://github.com/Azure/aks-set-context)

- [k8s-создать-секрет](https://github.com/Azure/k8s-create-secret)

- [k8s-развертывание](https://github.com/Azure/k8s-deploy)

- [веб-приложения-контейнер-развертывание](https://github.com/Azure/webapps-container-deploy)

- [действия-образцы рабочего процесса](https://github.com/Azure/actions-workflow-samples)

---
title: Развертывание экземпляра контейнеров по действию GitHub
description: Настройте действие GitHub, которое автоматизирует шаги для создания, нажатия и развертывания изображения контейнера в экземплярах контейнеров Azure
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258045"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Настройка действия GitHub для создания экземпляра контейнера

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) — это набор функций в GitHub для автоматизации рабочих процессов разработки программного обеспечения в том же месте, где вы храните код и сотрудничаете с запросами и проблемами.

Используйте развертывание для [azure Контейнер instances](https://github.com/azure/aci-deploy) GitHub действий для автоматизации развертывания контейнера для Azure контейнера instances. Действие позволяет настроить свойства для экземпляра контейнера, аналогичного тем, которые в [контейнере az создают][az-container-create] команду.

В этой статье показано, как настроить рабочий процесс в репо GitHub, выполняя следующие действия:

* Создание образа с помощью Dockerfile
* Нажмите изображение в реестр контейнеров Azure
* Развертывание изображения контейнера в экземпляр контейнера Azure

В этой статье показаны два способа настройки рабочего процесса:

* Настройте рабочий процесс самостоятельно в репо GitHub с помощью действий «Развертывание контейнерных инстанций» и других действий.  
* Используйте `az container app up` команду в [развертывании](https://github.com/Azure/deploy-to-azure-cli-extension) для расширения Azure в Azure CLI. Эта команда упрощает создание рабочего процесса GitHub и этапов развертывания.

> [!IMPORTANT]
> В настоящее время проводится предварительный просмотр действий GitHub для контейнерных инстанций Azure. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="prerequisites"></a>Предварительные требования

* **Учетная запись GitHub** https://github.com - Создайте учетную запись, если у вас ее еще нет.
* **Azure CLI** - Для выполнения шагов Azure CLI можно использовать оболочку Azure Cloud Shell или локальную установку Azure CLI. Если вам нужно установить или обновить, [см.][azure-cli-install]
* **Реестр контейнеров Azure** - Если его нет, создайте реестр контейнеров Azure в базовом уровне с помощью портала [Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [azure](../container-registry/container-registry-get-started-portal.md)или других методов. Обратите внимание на группу ресурсов, используемую для развертывания, которая используется для рабочего процесса GitHub.

## <a name="set-up-repo"></a>Настройка репо

* Для примеров в этой статье используйте GitHub для развилки следующего репозитория:https://github.com/Azure-Samples/acr-build-helloworld-node

  Это репо содержит Dockerfile и исходные файлы для создания изображения контейнера небольшого веб-приложения.

  ![Снимок экрана с выделенной кнопкой "Fork" (Создать вилку) в GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Убедитесь, что действия включены для вашего репозитория. Перейдите к раздвентивному репозиторию и выберите**Действия** **настроек.** >  В **разрешениях действий**гарантируется, что для **этого репозитория** будут выбраны локальные и сторонние действия.

## <a name="configure-github-workflow"></a>Настройка рабочего процесса GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Создание основного сервиса для проверки подлинности Azure

В рабочем процессе GitHub необходимо предоставить учетные данные Azure для проверки подлинности в Azure CLI. Следующий пример создает основную службу с ролью contributor, отнесемым группе ресурсов для вашего реестра контейнеров.

Во-первых, получите идентификатор ресурсов группы ресурсов. Заменить название вашей группы в следующей команде [группы az:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Используйте [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] для создания основного сервиса:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Она выводит выходные данные следующего вида:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Сохранить выход JSON, поскольку он используется на более позднем этапе. Кроме того, принять `clientId`к сведению , которые вам нужно обновить основной службы в следующем разделе.

### <a name="update-service-principal-for-registry-authentication"></a>Основной сервис обновления для проверки подлинности реестра

Обновление основных учетных данных службы Azure, чтобы разрешить выдачу разрешений в реестре контейнеров. Этот шаг позволяет рабочему процессу GitHub использовать принцип обслуживания для [проверки подлинности с вашим реестром контейнеров.](../container-registry/container-registry-auth-service-principal.md) 

Получите идентификатор ресурсов в контейнерном реестре. Замените название вашего реестра в следующей команде [шоу az acr:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Используйте [назначение роли az для][az-role-assignment-create] присвоения роли AcrPush, которая дает толчок и вытягивание доступа к реестру. Заменить идентификатор клиента вашего директора по обслуживанию:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Сохранение учетных данных для репо GitHub

1. В uI GitHub перейдите к раздвентивному репозиторию и выберите**секреты** **настроек.** >  

1. Выберите **Добавить новый секрет,** чтобы добавить следующие секреты:

|Секрет  |Значение  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Весь выход JSON из основного творения службы |
|`REGISTRY_LOGIN_SERVER`   | Имя сервера входа в реестр (все в нижнем регистре). Пример: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  Выход `clientId` из jSON из основного творения службы       |
|`REGISTRY_PASSWORD`     |  Выход `clientSecret` из jSON из основного творения службы |
| `RESOURCE_GROUP` | Имя группы ресурсов, используемой для охвата основного обслуживания |

### <a name="create-workflow-file"></a>Создание файла рабочего процесса

1. В uI GitHub выберите **действия** > **Новый рабочий процесс.**
1. Выберите **рабочий процесс самостоятельно.**
1. При **отожобье нового файла**вставьте следующее содержимое YAML, чтобы перезаписать пример кода. Примите имя `main.yml`файла по умолчанию или предоставьте выберите имя файла.
1. Выберите **Start commit,** дополнительно предоставьте краткое и расширенное описание вашего коммит, и выберите **Новый файл Commit.**

```yml
on: [push]
name: Linux_Container_Workflow

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
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Проверка рабочего процесса

После фиксации файла рабочего процесса срабатывает рабочий процесс. Чтобы просмотреть ход выполнения рабочего процесса, перейдите к**рабочим потокам** **действий.** >  

![Просмотр хода работопривки](./media/container-instances-github-action/github-action-progress.png)

Просканируйте [управление рабочим процессом](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) для получения информации о просмотре состояния и результатов каждого шага в рабочем процессе.

Когда рабочий процесс завершится, получите информацию о экземпляре контейнера под названием *aci-sampleapp,* запустив команду [шоу-шоу контейнера az.][az-container-show] Заменить название группы ресурсов: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Она выводит выходные данные следующего вида:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

После того, как экземпляр подготовлен, перейдите к F-DN контейнера в браузере, чтобы просмотреть запущенное веб-приложение.

![Запуск веб-приложения в браузере](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Используйте развертывание для расширения Azure

Кроме того, для настройки рабочего процесса используйте [расширение Deploy to Azure](https://github.com/Azure/deploy-to-azure-cli-extension) в Azure CLI. Команда `az container app up` в расширении принимает параметры ввода от вас для настройки рабочего процесса для развертывания в экземплярах контейнеров Azure. 

Рабочий процесс, созданный Azure CLI, аналогичен рабочему процессу, который можно [создать вручную с помощью GitHub.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>Дополнительная предпосылка

В дополнение к [предпосылкам](#prerequisites) и [настройке репо](#set-up-repo) для этого сценария необходимо установить **расширение Deploy to Azure** для Azure CLI.

Выполнить [команду расширения az][az-extension-add] для установки расширения:

```azurecli
az extension add \
  --name deploy-to-azure
```

Для получения информации о поиске, установке и управлении расширениями [см.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>Выполнить `az container app up`

Чтобы запустить [приложение аз контейнер ажиотаж до][az-container-app-up] команды, укажите как минимум:

* Название вашего реестра контейнеров Azure, например, *myregistry*
* URL-адрес репо GitHub, например,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Пример команды:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Прогресс командования

* По запросу предоставьте учетные данные GitHub или предоставьте [токен личного доступа GitHub](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT), который имеет *репо* *и* пользовательские области для проверки подлинности в вашем реестре. Если вы предоставляете учетные данные GitHub, команда создает PAT для вас.

* Команда создает секреты репо для рабочего процесса:

  * Основные учетные данные службы для Azure CLI
  * Учетные данные для доступа к реестру контейнеров Azure

* После того, как команда фиксирует файл рабочего процесса в репо, рабочий процесс срабатывает. 

Она выводит выходные данные следующего вида:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Проверка рабочего процесса

Рабочий процесс развертывает экземпляр контейнера Azure с базовым именем репо GitHub, в данном случае *acr-build-helloworld-узел.* В браузере вы можете просматривать ссылку, предоставляемую для просмотра запущенного веб-приложения. Если ваше приложение слушается в порту, кроме 8080, укажите это в URL вместо этого.

Для просмотра состояния рабочего процесса и результатов каждого шага [Managing a workflow run](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)в ui GitHub см.

## <a name="clean-up-resources"></a>Очистка ресурсов

Остановите экземпляр контейнера с помощью команды [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Чтобы удалить группу ресурсов и все ресурсы в ней, запустите команду [удаления группы az:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите [GitHub Marketplace](https://github.com/marketplace?type=actions) для получения дополнительных действий для автоматизации рабочего процесса разработки


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up

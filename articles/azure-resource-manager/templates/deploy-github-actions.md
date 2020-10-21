---
title: Развертывание шаблонов Resource Manager с помощью действий GitHub
description: Описывает развертывание шаблонов Azure Resource Manager с помощью действий GitHub.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure,subject-armqs
ms.openlocfilehash: f982ecd208dfd30757050df48c783718ed2b917a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282847"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Развертывание шаблонов Resource Manager с помощью действий GitHub

[Действия GitHub](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) — это набор функций в GitHub для автоматизации рабочих процессов разработки программного обеспечения в том же месте, где вы храните код и работаете над запросами на вытягивание и проблемами.

Используйте [действие развертывание Azure Resource Manager шаблона](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) для автоматизации развертывания шаблона диспетчер ресурсов в Azure. 

## <a name="prerequisites"></a>предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  
    - Репозиторий GitHub для хранения шаблонов диспетчер ресурсов и файлов рабочего процесса. Чтобы создать его, см. раздел [Создание нового репозитория](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Общие сведения о файле рабочего процесса

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса.

Файл содержит два раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определите субъект-службу. <br /> 2. Создайте секрет GitHub. |
|**Развертывание** | 1. разверните шаблон диспетчер ресурсов. |

## <a name="generate-deployment-credentials"></a>Создать учетные данные развертывания


Вы можете создать [субъект-службу](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) в [Azure CLI](/cli/azure/). Выполните эту команду с [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

Создайте группу ресурсов, если она еще не создана. 

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

Замените заполнитель `myApp` именем своего приложения. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

В приведенном выше примере Замените заполнители ИДЕНТИФИКАТОРом подписки и именем группы ресурсов. Выходные данные — это объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению службы приложений, как показано ниже. Скопируйте этот объект JSON для последующего использования. Вам понадобятся только разделы со `clientId` значениями,, `clientSecret` `subscriptionId` и `tenantId` . 

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
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Область в предыдущем примере ограничена группой ресурсов.



## <a name="configure-the-github-secrets"></a>Настройка секретов GitHub

Необходимо создать секреты для учетных данных Azure, группы ресурсов и подписок. 

1. В [GitHub](https://github.com/)найдите свой репозиторий.

1. Выберите **параметры > секреты > новый секрет**.

1. Вставьте все выходные данные JSON из команды Azure CLI в поле значения секрета. Присвойте этому имени секрет `AZURE_CREDENTIALS` .

1. Создайте другой секрет с именем `AZURE_RG` . Добавьте имя группы ресурсов в поле значения секрета (пример: `myResourceGroup` ). 

1. Создайте дополнительный секрет с именем `AZURE_SUBSCRIPTION` . Добавьте идентификатор подписки в поле значения секрета (пример: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ). 

## <a name="add-resource-manager-template"></a>Добавление шаблона Resource Manager

Добавьте шаблон диспетчер ресурсов в репозиторий GitHub. Этот шаблон создает учетную запись хранения.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Файл можно разместить в любом месте репозитория. В примере рабочего процесса в следующем разделе предполагается, что файл шаблона называется **azuredeploy.jsв**и хранится в корне репозитория.

## <a name="create-workflow"></a>Создание рабочего процесса

Файл рабочего процесса должен храниться в папке **. GitHub/Workflows** в корне репозитория. Расширение файла рабочего процесса может быть либо **.yml**, либо **.yaml**.

1. В репозитории GitHub выберите **Действия** в верхнем меню.
1. Выберите **Новый рабочий процесс**.
1. Выберите **настроить рабочий процесс вручную**.
1. Переименуйте файл рабочего процесса, если вы предпочитаете другое имя, отличное от **main.yml**. Например: **deployStorageAccount.yml**.
1. Замените содержимое YML-файла приведенным ниже:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@master

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS 
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Вместо этого можно указать файл параметров формата JSON в действии развертывания ARM (пример: `.azuredeploy.parameters.json` ).  

    Первый раздел файла рабочего процесса включает в себя:

    - **name**. Имя рабочего процесса.
    - **on**. Имя событий GitHub, которые инициируют рабочий процесс. Рабочий процесс инициируется при наличии события отправки в главной ветви, которое изменяет по крайней мере один из двух указанных файлов. Два файла — это файл рабочего процесса и файл шаблона.

1. Выберите **Начать фиксацию**.
1. Выберите **Зафиксировать непосредственно в главной ветви**.
1. Выберите **Зафиксировать новый файл** (или **Зафиксировать изменения**).

Так как рабочий процесс настроен на активацию файлом рабочего процесса или обновлением файла шаблона, рабочий процесс запускается сразу после фиксации изменений.

## <a name="check-workflow-status"></a>Проверка состояния рабочего процесса

1. Перейдите на вкладку **действия** . Вы увидите рабочий процесс **CREATE деплойсторажеаккаунт. yml** , указанный в списке. Выполнение рабочего процесса занимает 1-2 минут.
1. Выберите рабочий процесс, чтобы открыть его.
1. Выберите **выполнить развертывание ARM** в меню, чтобы проверить развертывание.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов и репозиторий больше не требуются, очистите развернутые ресурсы, удалив группу ресурсов и репозиторий GitHub. 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание первого шаблона ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

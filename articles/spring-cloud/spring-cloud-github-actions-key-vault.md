---
title: Проверка подлинности Azure Веснного облака с Key Vault в действиях GitHub
description: Как использовать хранилище ключей с рабочим процессом CI/CD для Azure Веснного облака с действиями GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945467"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Проверка подлинности Azure Веснного облака с Key Vault в действиях GitHub
Хранилище ключей — это безопасное место для хранения ключей. Корпоративным пользователям необходимо хранить учетные данные для сред CI/CD в области, в которой они контролируются. Ключ для получения учетных данных в хранилище ключей должен быть ограничен областью действия ресурса.  Он имеет доступ только к области хранилища ключей, а не ко всей области Azure. Он похож на ключ, который может открыть только надежное поле, а не главный ключ, который может открыть все двери в здании. Это способ получить ключ с помощью другого ключа, который полезен в рабочем процессе CICD. 

## <a name="generate-credential"></a>Создание учетных данных
Чтобы создать ключ для доступа к хранилищу ключей, выполните следующую команду на локальном компьютере:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Область, заданная параметром `--scopes`, ограничивает доступ к ресурсу по ключу.  Он может получить доступ только к надежному полю.

С результатами:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Затем сохраните результаты в **секреты** GitHub, как описано в разделе [Настройка репозитория GitHub и аутентификация в Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Добавление политик доступа для учетных данных
Созданные выше учетные данные могут получать только общие сведения о Key Vault, а не содержимое, которое она хранит.  Чтобы получить секреты, хранящиеся в Key Vault, необходимо задать политики доступа для учетных данных.

Перейдите на панель мониторинга **Key Vault** в портал Azure, щелкните меню **управления доступом** , а затем откройте вкладку **назначения ролей** . Выберите **приложения** для **типа** и `This resource` для **области**.  Вы должны увидеть учетные данные, созданные на предыдущем шаге:

 ![Настройка политики доступа](./media/github-actions/key-vault1.png)

Скопируйте имя учетных данных, например `azure-cli-2020-01-19-04-39-02`. Откройте меню **политики доступа** , щелкните ссылку **+ Добавить политику доступа** .  Выберите `Secret Management` для **шаблона**, а затем выберите **субъект**. Вставьте имя учетных данных в **основное**/**выберите** поле ввода:

 ![Выберите пункт](./media/github-actions/key-vault2.png)

 Нажмите кнопку **Добавить** в диалоговом окне **Добавление политики доступа** , а затем нажмите кнопку **сохранить**.

## <a name="generate-full-scope-azure-credential"></a>Создание учетных данных Azure с полной областью действия
Это главный ключ для открытия всех дверей в здании. Процедура аналогична предыдущему шагу, но здесь мы изменим область для создания главного ключа:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Опять же, результаты:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Скопируйте целую строку JSON.  Вернуться к панели мониторинга **Key Vault** . Откройте меню **секреты** и нажмите кнопку **создать/импортировать** . Введите имя секрета, например `AZURE-CRENDENTIALS-FOR-SPRING`. Вставьте строку учетных данных JSON в поле ввода **значения** . Можно заметить, что поле ввода значения является однострочным текстовым полем, а не многострочной текстовой областью.  Здесь можно вставить полную строку JSON.

 ![Учетные данные полной области действия](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Объединение учетных данных в действиях GitHub
Задайте учетные данные, используемые при выполнении конвейера CICD:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Дальнейшие действия
* [Действия весны GitHub Cloud](./spring-cloud-howto-github-actions.md)
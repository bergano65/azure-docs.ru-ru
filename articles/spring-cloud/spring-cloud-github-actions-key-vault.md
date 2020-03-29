---
title: Authenticate Весеннее облако Azure с помощью Key Vault в действиях GitHub
description: Как использовать хранилище ключей с ci/CD рабочим процессом для облачности Azure Spring с действиями GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945467"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Authenticate Весеннее облако Azure с помощью Key Vault в действиях GitHub
Хранилище ключей является безопасным местом для хранения ключей. Корпоративным пользователям необходимо хранить учетные данные для сред CI/CD в области, которую они контролируют. Ключ для получения учетных данных в хранилище ключей должен быть ограничен областью ресурсов.  Он имеет доступ только к области хранилища ключей, а не ко всей области Azure. Это как ключ, который может открыть только сильную коробку не мастер-ключ, который может открыть все двери в здании. Это способ получить ключ с другим ключом, который полезен в рабочем процессе CICD. 

## <a name="generate-credential"></a>Создание учетных данных
Чтобы создать ключ для доступа к хранилищу ключей, выполните приведенную ниже команду на локальной машине:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Область, указанная `--scopes` параметром, ограничивает ключевой доступ к ресурсу.  Он может получить доступ только к сильной коробке.

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
Затем сохраните результаты до **секретов** GitHub, описанных в [настройке репозитория GitHub и аутентификации с помощью Azure.](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)

## <a name="add-access-policies-for-the-credential"></a>Добавление политик доступа к учетных данных
Учетные данные, созданные выше, могут получать только общую информацию о Хранилище ключей, а не содержимое, в нем он хранится.  Чтобы получить секреты, хранящиеся в Убежище ключей, необходимо настроить политики доступа к учетным данным.

Перейдите на панель мониторинга **Key Vault** на портале Azure, нажмите на **Apps** меню **Type** управления `This resource` **scope** **доступом,** а затем откройте вкладку **«Назначения ролей.**  Вы должны увидеть учетные данные, созданные на предыдущем этапе:

 ![Установка политики доступа](./media/github-actions/key-vault1.png)

Например, `azure-cli-2020-01-19-04-39-02`копируйте имя учетных данных. Откройте меню **политик доступа,** щелкните по ссылке **«Дополнительная политика доступа».**  Выберите `Secret Management` для **шаблона**, затем выберите **основных**. Вставьте имя учетных данных в поле ввода **основного**/**выбора:**

 ![Выберите пункт](./media/github-actions/key-vault2.png)

 Нажмите кнопку **Добавить** в диалоге **политики добавления доступа,** а затем нажмите **Сохранить**.

## <a name="generate-full-scope-azure-credential"></a>Создание полноформатных учетных данных Azure
Это главный ключ, чтобы открыть все двери в здании. Процедура аналогична предыдущему этапу, но здесь мы изменяем область для создания основного ключа:

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
Копируйте всю строку JSON.  Вернуться к панели мониторинга **Key Vault.** Откройте меню **Secrets,** а затем нажмите кнопку **Generate/Import.** Ввейте секретное `AZURE-CRENDENTIALS-FOR-SPRING`имя, например . Вставьте строку учетных данных JSON в поле ввода **значения.** Вы можете заметить, что входное поле значения является полем текста одной строки, а не многолинейной областью текста.  Вы можете вставить полную строку JSON там.

 ![Полный учетный учетный данный](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Объедините учетные данные в действиях GitHub
Установите учетные данные, используемые при выполнении конвейера CICD:

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
* [Весенние действия Облака GitHub](./spring-cloud-howto-github-actions.md)
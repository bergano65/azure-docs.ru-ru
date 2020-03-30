---
title: Шифрование источника приложения в состоянии покоя
description: Шифрование данных приложения в Хранилище Azure и развертывание их в виде файла пакета.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408749"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Шифрование в состоянии покоя с помощью ключей, управляемых клиентом

Шифрование данных приложения вашего веб-приложения в состоянии покоя требует учетной записи хранения Azure и убежища ключей Azure. Эти службы используются при запуске приложения из пакета развертывания.

  - [Azure Storage обеспечивает шифрование в состоянии покоя.](../storage/common/storage-service-encryption.md) Вы можете использовать системные ключи или свои собственные, управляемые клиентом ключи. Здесь хранятся данные приложения, когда они не работают в веб-приложении Azure.
  - [Запуск из пакета развертывания](deploy-run-package.md) — это функция развертывания службы app. Это позволяет развертывать содержимое сайта из учетной записи хранения Azure с помощью URL-адреса общей подписи доступа (SAS).
  - [Ссылки Key Vault](app-service-key-vault-references.md) являются функцией безопасности службы безопасности. Это позволяет импортировать секреты во время выполнения в качестве настроек приложения. Используйте это для шифрования URL SAS учетной записи хранения Azure.

## <a name="set-up-encryption-at-rest"></a>Настройка шифрования в состоянии покоя

### <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Во-первых, [создайте учетную запись Azure Storage](../storage/common/storage-account-create.md) и [зашифруйте ее ключами, управляемыми клиентом.](../storage/common/encryption-customer-managed-keys.md) После создания учетной записи хранилища используйте [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) для загрузки файлов пакетов.

Затем используйте исследователь хранения для [создания SAS.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> Сохранить этот URL SAS, это используется позже, чтобы обеспечить безопасный доступ к пакету развертывания во время выполнения.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Настройка из пакета из учетной записи хранилища
  
После загрузки файла в хранилище Blob и получения URL-адреса SAS для файла установите `WEBSITE_RUN_FROM_PACKAGE` настройку приложения в URL SAS. Следующий пример делает это с помощью Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Добавление этой настройки приложения приводит к перезапуску веб-приложения. После того, как приложение перезаработает, просмотрите его и убедитесь, что приложение начало правильно использовать пакет развертывания. Если приложение не начало правильно, см. [Run from package troubleshooting guide](deploy-run-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Шифрование настройки приложения с помощью ссылок Key Vault

Теперь можно заменить `WEBSITE_RUN_FROM_PACKAGE` значение настройки приложения ссылкой на Ключевое Убежище на закодированный SAS URL. Это сохраняет SAS URL зашифрованным в Key Vault, что обеспечивает дополнительный уровень безопасности.

1. Используйте [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) следующую команду для создания экземпляра Убежища ключей.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Следуйте [этим инструкциям, чтобы предоставить вашему приложению доступ к](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) хранилищу ключей:

1. Используйте [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) следующую команду, чтобы добавить внешний URL в качестве секрета в хранилище ключей:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Используйте [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) следующую команду `WEBSITE_RUN_FROM_PACKAGE` для создания настройки приложения со значением в качестве ссылки Key Vault на внешний URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Будет `<secret-version>` находиться в выводе `az keyvault secret set` предыдущей команды.

Обновление этой настройки приложения приводит к перезапуску веб-приложения. После того, как приложение перезапустилось, просмотрите его, убедитесь, что оно начало правильно использовать ссылку Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Как повернуть токен доступа

Лучше всего периодически поворачивать ключ SAS в вашей учетной записи хранения. Чтобы обеспечить непреднамеренное свободное доступ к веб-приложению, необходимо также обновить URL SAS в Key Vault.

1. Поверните ключ SAS, перенастроившись на учетную запись хранилища на портале Azure. Под **клавишами «Настройки** > **доступа»** щелкните значок, чтобы повернуть клавишу SAS.

1. Копируйте новый URL SAS и используйте следующую команду для установки обновленного URL-адреса SAS в хранилище ключей:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Обновите ссылку на хранилище ключей в настройках приложения в новую секретную версию:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Будет `<secret-version>` находиться в выводе `az keyvault secret set` предыдущей команды.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Как отменить доступ к данным веб-приложения

Существует два способа аннулирования доступа веб-приложения к учетной записи хранилища. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Поверните ключ SAS для учетной записи хранения Azure

Если ключ SAS для учетной записи хранилища повернут, веб-приложение больше не будет иметь доступа к учетной записи хранилища, но оно будет продолжать работать с последней загруженной версией файла пакета. Перезапустите веб-приложение, чтобы очистить последнюю загруженную версию.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Удалите доступ веб-приложения к Key Vault

Вы можете отозвать доступ веб-приложения к данным сайта, отключив доступ веб-приложения к Key Vault. Для этого удалите политику доступа для идентификации веб-приложения. Это та же личность, что и ранее при настройке ссылок на хранилище ключей.

## <a name="summary"></a>Сводка

Файлы приложения теперь шифруются в состоянии покоя в учетной записи хранилища. Когда ваше веб-приложение запускается, оно извлекает URL SAS из хранилища ключей. Наконец, веб-приложение загружает файлы приложения из учетной записи хранилища. 

Если необходимо отозвать доступ веб-приложения к учетной записи хранилища, вы можете либо отозвать доступ к хранилищу ключей, либо повернуть ключи учетной записи хранилища, что делает недействительным URL SAS.

## <a name="frequently-asked-questions"></a>Вопросы и ответы

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Есть ли дополнительная плата за запуск веб-приложения из пакета развертывания?

Только стоимость, связанная с учетной записью хранения Azure и любыми применимыми расходами на выход.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Как запуск пакета развертывания влияет на мое веб-приложение?

- Запуск приложения из пакета `wwwroot/` развертывания делает чтение только. Ваше приложение получает ошибку при попытке написать в этот каталог.
- Форматы TAR и GZIP не поддерживаются.
- Эта функция не совместима с локальным кэшем.

## <a name="next-steps"></a>Дальнейшие действия

- [Ссылки На Key Vault для службы приложений](app-service-key-vault-references.md)
- [Шифрование хранилища Azure для данных в состоянии покоя](../storage/common/storage-service-encryption.md)

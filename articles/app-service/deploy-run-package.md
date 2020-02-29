---
title: Запуск приложения из ZIP-пакета
description: Развертывание ZIP-пакета приложения с атомарностью. Повысьте предсказуемость и надежность работы приложения во время процесса развертывания ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920728"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Запуск приложения в службе приложений Azure непосредственно из ZIP-пакета

В [службе приложений Azure](overview.md)приложения можно запускать непосредственно из файла пакета развертывания ZIP. В этой статье показано, как включить эту функцию в приложении.

Все остальные методы развертывания в службе приложений имеют что-то общее: файлы развертываются в *D:\home\site\wwwroot* в приложении (или */Хоме/Сите/ввврут* для приложений Linux). Так как в среде выполнения приложение использует тот же каталог, развертывание может завершиться неудачей из-за конфликтов блокировки файлов, и приложение может вести себя непредсказуемо, так как некоторые файлы еще не обновлены.

В отличие от этого, при запуске непосредственно из пакета файлы в пакете не копируются в каталог *wwwroot* . Вместо этого сам ZIP-пакет монтируется непосредственно в каталог *wwwroot* только для чтения. Существует несколько преимуществ, выполняемых непосредственно из пакета.

- Устраняет конфликты блокировки файлов между развертыванием и средой выполнения.
- Гарантирует, что в любое время выполняются только полностью развернутые приложения.
- Можно выполнить развертывание в рабочее приложение (с помощью перезапуска).
- Повышает производительность Azure Resource Manager развертываний.
- Может сократиться время "холодного запуска", особенно для функций JavaScript с большими деревьями пакетов npm.

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакетов.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Включить Запуск из пакета

Параметр приложения `WEBSITE_RUN_FROM_PACKAGE` включает запуск из пакета. Чтобы задать его, выполните следующую команду с Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` позволяет запускать приложение из пакета, который является локальным для приложения. Можно также [запустить из удаленного пакета](#run-from-external-url-instead).

## <a name="run-the-package"></a>Запуск пакета

Проще всего запустить пакет в службе приложений с помощью команды Azure CLI [AZ webapp Deployment Source config-ZIP](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) . Например:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Так как параметр приложения `WEBSITE_RUN_FROM_PACKAGE` задан, эта команда не извлекает содержимое пакета в каталог *D:\home\site\wwwroot* приложения. Вместо этого он передает ZIP-файл "как есть" в *д:\хоме\дата\ситепаккажес*и создает *packagename. txt* в том же каталоге, который содержит имя пакета zip для загрузки во время выполнения. При отправке ZIP-пакета другим способом (например, [FTP](deploy-ftp.md)) необходимо вручную создать каталог *д:\хоме\дата\ситепаккажес* и файл *packagename. txt* .

Команда также перезапускает приложение. Поскольку `WEBSITE_RUN_FROM_PACKAGE` установлен, служба приложений подключает отправленный пакет как каталог *wwwroot* только для чтения и запускает приложение непосредственно из этого подключенного каталога.

## <a name="run-from-external-url-instead"></a>Запустить из внешнего URL-адреса

Вы также можете запустить пакет из внешнего URL-адреса, например хранилища больших двоичных объектов Azure. Можно использовать [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи файлов пакета в учетную запись хранения больших двоичных объектов. Для безопасного доступа к пакету среда выполнения службы приложений должна использовать частный контейнер хранилища с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) . 

Когда вы отправите файл в хранилище BLOB-объектов и получите в нем URL для SAS, задайте для параметра приложения `WEBSITE_RUN_FROM_PACKAGE` значение URL-адрес. В следующем примере он выполняется с помощью Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

При публикации обновленного пакета с тем же именем в хранилище BLOB-объектов необходимо перезапустить приложение, чтобы обновленный пакет загружался в службу приложений.

### <a name="use-key-vault-references"></a>Использование Key Vault ссылок

Для повышения безопасности можно использовать ссылки Key Vault в сочетании с внешним URL-адресом. Это обеспечивает шифрование неактивных URL-адресов и позволяет использовать Key Vault для управления секретами и их смены. Рекомендуется использовать хранилище BLOB-объектов Azure, чтобы можно было легко поворачивать соответствующий ключ SAS. Хранилище BLOB-объектов Azure шифруется при хранении, что обеспечивает безопасность данных приложения, если оно не развернуто в службе приложений.

1. Создайте хранилище ключей Azure.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Добавьте внешний URL-адрес в качестве секрета в Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Создайте параметр приложения `WEBSITE_RUN_FROM_PACKAGE` и задайте в качестве значения Key Vault ссылку на внешний URL-адрес.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Дополнительные сведения см. в следующих статьях.

- [Key Vault ссылки для службы приложений](app-service-key-vault-references.md)
- [Шифрование неактивных данных в службе хранилища Azure](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Диагностика

- Выполнение непосредственно из пакета делает `wwwroot` только для чтения. При попытке записи файлов в этот каталог приложение получит сообщение об ошибке.
- Форматы TAR и GZIP не поддерживаются.
- Эта функция несовместима с [локальным кэшем](overview-local-cache.md).
- Для повышения производительности холодного запуска используйте параметр local ZIP (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="more-resources"></a>Дополнительные ресурсы

- [Непрерывное развертывание для службы приложений Azure](deploy-continuous-deployment.md)
- [Развертывание кода с помощью ZIP-файла или с файлом WAR](deploy-zip.md)

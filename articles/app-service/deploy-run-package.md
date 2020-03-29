---
title: Выполнить приложение из пакета с почтовым индексом
description: Развертывание пакета вашего приложения с атомарностью. Улучшить предсказуемость и надежность поведения вашего приложения в процессе развертывания.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920728"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Запустите приложение в службе приложений Azure непосредственно из пакета с почтовым индексом

В [Службе приложений Azure](overview.md)вы можете запускать приложения непосредственно из файла пакета развертывания. В этой статье показано, как включить эту функциональность в приложении.

Все другие методы развертывания в Службе App имеют нечто общее: ваши файлы развернуты в *D: «домашний сайт»wwwroot* в вашем приложении (или */дом/сайт/wwwroot* для приложений Linux). Поскольку тот же каталог используется приложением во время выполнения, возможно, что развертывание сбой из-за конфликтов блокировки файлов, и для приложения вести себя непредсказуемо, потому что некоторые файлы еще не обновлены.

В отличие от этого, когда вы работаете непосредственно из пакета, файлы в пакете не копируются в каталог *wwwroot.* Вместо этого, сам пакет зип устанавливается непосредственно как только читать *каталог wwwroot.* Существует несколько преимуществ для запуска непосредственно из пакета:

- Устраняет конфликты блокировки файлов между развертыванием и временем выполнения.
- Гарантирует, что в любое время работают только полноразвернутые приложения.
- Можно выполнить развертывание в рабочее приложение (с помощью перезапуска).
- Повышается производительность развертываний Azure Resource Manager.
- Может сократиться время "холодного запуска", особенно для функций JavaScript с большими деревьями пакетов npm.

> [!NOTE]
> В настоящее время поддерживаются только файлы пакетов с почтовым индексом.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Включить запуск из пакета

Настройка `WEBSITE_RUN_FROM_PACKAGE` приложения позволяет работать из пакета. Чтобы установить его, запустите следующую команду с Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`позволяет запускать приложение из локального пакета в приложение. Вы также можете [запустить из удаленного пакета.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Запуск пакета

Самый простой способ запустить пакет в службе приложений — это с помощью [команды конфигурации веб-приложений](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) Azure CLI az webapp. Пример:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Поскольку `WEBSITE_RUN_FROM_PACKAGE` настройка приложения установлена, эта команда не извлекает содержимое пакета в каталог *D: «домашний» каталог вашего* приложения. Вместо этого, он загружает файл как это до *D: «домашние данные»SitePackages*, и создает *packagename.txt* в том же каталоге, который содержит имя пакета ЗИП для загрузки во время выполнения. Если вы загружаете свой пакет по-другому (например, [FTP),](deploy-ftp.md)вам необходимо создать каталог *D: «домашние данные»SitePackages* и файл *package.txt packages* вручную.

Команда также перезапускает приложение. Поскольку `WEBSITE_RUN_FROM_PACKAGE` app Service устанавливает загруженный пакет в виде каталога *wwwroot* только для чтения, и запускает приложение непосредственно из этого установленного каталога.

## <a name="run-from-external-url-instead"></a>Выполнить из внешнего URL вместо

Можно также запустить пакет из внешнего URL-адреса, например, Azure Blob Storage. Можно использовать [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи файлов пакета в учетную запись хранения больших двоичных объектов. Для обеспечения безопасного доступа [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) к пакету службы ожидания службы app Service необходимо использовать личный контейнер хранения данных. 

После загрузки файла в хранилище Blob и получения URL-адреса SAS для файла установите настройку `WEBSITE_RUN_FROM_PACKAGE` приложения на URL-. Следующий пример делает это с помощью Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Если вы публикуете обновленный пакет с тем же именем для хранения Blob, вам необходимо перезапустить приложение, чтобы обновленный пакет был загружен в Службу приложений.

## <a name="troubleshooting"></a>Устранение неполадок

- Запуск непосредственно из `wwwroot` пакета делает только для чтения. Ваше приложение получит ошибку, если попытается написать файлы в этот каталог.
- Форматы TAR и GZIP не поддерживаются.
- Эта функция не совместима с [локальным кэшом.](overview-local-cache.md)
- Для повышения производительности холодного старта используйте локальный опцион «Зип» (No1).`WEBSITE_RUN_FROM_PACKAGE`

## <a name="more-resources"></a>Дополнительные ресурсы

- [Непрерывное развертывание для службы приложений Azure](deploy-continuous-deployment.md)
- [Развертывание кода с файлом «ЗИП» или «Война»](deploy-zip.md)

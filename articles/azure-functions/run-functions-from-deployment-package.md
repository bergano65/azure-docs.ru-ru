---
title: Run your Azure Functions from a package
description: Настройте запуск функций в среде выполнения Функций Azure путем подключения файла пакета развертывания, содержащего файлы проекта приложения-функции.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: f5d3465e0899f7e5eab213bdb6234313128b7ec8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230355"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Запуск функций Azure из файла пакета

В Azure можно запустить функции непосредственно из файла пакета развертывания в приложении-функции. Другой вариант — развернуть файлы в каталоге `d:\home\site\wwwroot` приложения-функции.

В этой статье описываются преимущества запуска функций из пакета. Здесь также показано, как включить эту функцию в приложении-функции.

> [!IMPORTANT]
> When deploying your functions to a Linux function app in a [Premium plan](functions-scale.md#premium-plan), you should always run from the package file and [publish your app using the Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Преимущества запуска из файла пакета
  
Запуск функции из файла пакета обеспечивает несколько преимуществ:

+ Снижается риск возникновения проблем из-за блокировки копирования файлов.
+ Можно выполнить развертывание в рабочее приложение (с помощью перезапуска).
+ Обеспечивается надежность файлов, выполняемых в приложении.
+ Повышается производительность [развертываний Azure Resource Manager](functions-infrastructure-as-code.md).
+ Может сократиться время "холодного запуска", особенно для функций JavaScript с большими деревьями пакетов npm.

Дополнительные сведения см. в [этом объявлении](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Настройка запуска функций из пакета

Чтобы настроить запуск приложения-функции из пакета, нужно просто добавить `WEBSITE_RUN_FROM_PACKAGE` в параметрах приложения-функции. Параметр `WEBSITE_RUN_FROM_PACKAGE` может иметь одно из следующих значений:

| Value  | Описание  |
|---------|---------|
| **`1`**  | Recommended for function apps running on Windows. Запуск из файла пакета в папке `d:\home\data\SitePackages` приложения-функции. If not [deploying with zip deploy](#integration-with-zip-deployment), this option requires the folder to also have a file named `packagename.txt`. Этот файл содержит только имя файла пакета в папке без каких-либо пробелов. |
|**`<URL>`**  | Расположение определенного файла пакета, который вы хотите запустить. При использовании хранилища BLOB-объектов следует использовать закрытый контейнер с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer), чтобы настроить доступ к пакету в среде выполнения функций. Можно использовать [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи файлов пакета в учетную запись хранения больших двоичных объектов. When you specify a URL, you must also [sync triggers](functions-deployment-technologies.md#trigger-syncing) after you publish an updated package. |

> [!CAUTION]
> When running a function app on Windows, the external URL option yields worse cold-start performance. When deploying your function app to Windows, you should set `WEBSITE_RUN_FROM_PACKAGE` to `1` and publish with zip deployment.

Далее показано приложение-функция, настроенное для запуска из ZIP-файла, размещенного в хранилище BLOB-объектов Azure:

![Параметр приложения WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакета.

## <a name="integration-with-zip-deployment"></a>Интеграция с помощью развертывания из ZIP-файла

[Zip deployment][Zip deployment for Azure Functions] is a feature of Azure App Service that lets you deploy your function app project to the `wwwroot` directory. Проект упакован как ZIP-файл развертывания. Те же API можно использовать для развертывания пакета в папку `d:\home\data\SitePackages`. Если для параметра приложения `WEBSITE_RUN_FROM_PACKAGE` задано значение `1`, API-интерфейсы развертывания из ZIP-файла копируют пакет в папку `d:\home\data\SitePackages`, а не извлекают файлы в `d:\home\site\wwwroot`. Также создается файл `packagename.txt`. After a restart, the package is mounted to `wwwroot` as a read-only filesystem. Дополнительные сведения о развертывании из ZIP-файла см. в статье [Непрерывное развертывание Функций Azure из ZIP-файла](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Добавление параметра WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

- Run From Package makes `wwwroot` read-only, so you will receive an error when writing files to this directory.
- Tar and gzip formats are not supported.
- This feature does not compose with local cache.
- For improved cold-start performance, use the local Zip option (`WEBSITE_RUN_FROM_PACKAGE`=1).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

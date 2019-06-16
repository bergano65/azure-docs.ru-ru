---
title: Запуск Функций Azure из пакета | Документация Майкрософт
description: Настройте запуск функций в среде выполнения Функций Azure путем подключения файла пакета развертывания, содержащего файлы проекта приложения-функции.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: glenga
ms.openlocfilehash: 88e5f1ac7834caa32302a3817e1779d0d733a7b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787545"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Запуск функций Azure из файла пакета

> [!NOTE]
> Возможности, описываемые в этой статье не доступен для приложения-функции на платформе Linux в [план службы приложений](functions-scale.md#app-service-plan).

В Azure можно запустить функции непосредственно из файла пакета развертывания в приложении-функции. Другой вариант — развернуть файлы в каталоге `d:\home\site\wwwroot` приложения-функции.

В этой статье описываются преимущества запуска функций из пакета. Здесь также показано, как включить эту функцию в приложении-функции.

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

| Значение  | Описание  |
|---------|---------|
| **`1`**  | Рекомендуется использовать функции приложения, работающие на Windows. Запуск из файла пакета в папке `d:\home\data\SitePackages` приложения-функции. В противном случае [развертывание с помощью zip развертывание](#integration-with-zip-deployment), этот параметр требует папке должен также иметь файл с именем `packagename.txt`. Этот файл содержит только имя файла пакета в папке без каких-либо пробелов. |
|**`<url>`**  | Расположение определенного файла пакета, который вы хотите запустить. При использовании хранилища BLOB-объектов следует использовать закрытый контейнер с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas), чтобы настроить доступ к пакету в среде выполнения функций. Можно использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для передачи файлов пакета в учетную запись хранения больших двоичных объектов.         |

> [!CAUTION]
> При запуске приложения-функции в Windows, внешний URL-адрес параметр дает хуже производительность при холодном запуске. При развертывании приложения-функции в Windows, необходимо задать `WEBSITE_RUN_FROM_PACKAGE` для `1` и публикации с помощью развертывания zip.

Далее показано приложение-функция, настроенное для запуска из ZIP-файла, размещенного в хранилище BLOB-объектов Azure:

![Параметр приложения WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакета.

## <a name="integration-with-zip-deployment"></a>Интеграция с помощью развертывания из ZIP-файла

[Развертывание из ZIP-файла][Zip deployment for Azure Functions] — это функция Службы приложений Azure, которая позволяет развертывать проект приложения-функции в каталог `wwwroot`. Проект упакован как ZIP-файл развертывания. Те же API можно использовать для развертывания пакета в папку `d:\home\data\SitePackages`. Если для параметра приложения `WEBSITE_RUN_FROM_PACKAGE` задано значение `1`, API-интерфейсы развертывания из ZIP-файла копируют пакет в папку `d:\home\data\SitePackages`, а не извлекают файлы в `d:\home\site\wwwroot`. Также создается файл `packagename.txt`. Затем после перезагрузки приложение-функция запускается из пакета и `wwwroot` становится доступным только для чтения. Дополнительные сведения о развертывании из ZIP-файла см. в статье [Непрерывное развертывание Функций Azure из ZIP-файла](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Добавление параметра WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Устранение неполадок

- Запуск из пакета делает `wwwroot` только для чтения, поэтому появится сообщение об ошибке при записи файлов в этот каталог.
- Не поддерживаются форматы tar-файл и gzip.
- Эта функция не используются совместно с локального кэша.
- Для повышения производительности холодного запуска, используйте параметр локального Zip (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

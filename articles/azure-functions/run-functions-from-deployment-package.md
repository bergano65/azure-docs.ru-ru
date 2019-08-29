---
title: Запуск Функций Azure из пакета | Документация Майкрософт
description: Настройте запуск функций в среде выполнения Функций Azure путем подключения файла пакета развертывания, содержащего файлы проекта приложения-функции.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: b6a2347ff79268cdaf54993952d59bd700b781bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095967"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Запуск функций Azure из файла пакета

В Azure можно запустить функции непосредственно из файла пакета развертывания в приложении-функции. Другой вариант — развернуть файлы в каталоге `d:\home\site\wwwroot` приложения-функции.

В этой статье описываются преимущества запуска функций из пакета. Здесь также показано, как включить эту функцию в приложении-функции.

> [!IMPORTANT]
> При развертывании функций в приложении-функции Linux в [плане Premium](functions-scale.md#premium-plan)следует всегда запускать из файла пакета и [публиковать приложение с помощью Azure functions Core Tools](functions-run-local.md#project-file-deployment).

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
| **`1`**  | Рекомендуется для приложений функций, работающих в Windows. Запуск из файла пакета в папке `d:\home\data\SitePackages` приложения-функции. Если развертывание не выполняется [с помощью ZIP-развертывания](#integration-with-zip-deployment), для этого параметра требуется, чтобы папка также `packagename.txt`соимела файл с именем. Этот файл содержит только имя файла пакета в папке без каких-либо пробелов. |
|**`<url>`**  | Расположение определенного файла пакета, который вы хотите запустить. При использовании хранилища BLOB-объектов следует использовать закрытый контейнер с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer), чтобы настроить доступ к пакету в среде выполнения функций. Можно использовать [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи файлов пакета в учетную запись хранения больших двоичных объектов.         |

> [!CAUTION]
> При запуске приложения-функции в Windows параметр External URL дает более серьезную производительность холодного запуска. При развертывании приложения-функции в Windows необходимо установить значение `WEBSITE_RUN_FROM_PACKAGE` `1` и опубликовать с помощью ZIP-развертывания.

Далее показано приложение-функция, настроенное для запуска из ZIP-файла, размещенного в хранилище BLOB-объектов Azure:

![Параметр приложения WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакета.

## <a name="integration-with-zip-deployment"></a>Интеграция с помощью развертывания из ZIP-файла

[Развертывание ZIP][Zip deployment for Azure Functions] — это функция службы приложений Azure, которая позволяет развертывать проект приложения-функции в `wwwroot` каталоге. Проект упакован как ZIP-файл развертывания. Те же API можно использовать для развертывания пакета в папку `d:\home\data\SitePackages`. Если для параметра приложения `WEBSITE_RUN_FROM_PACKAGE` задано значение `1`, API-интерфейсы развертывания из ZIP-файла копируют пакет в папку `d:\home\data\SitePackages`, а не извлекают файлы в `d:\home\site\wwwroot`. Также создается файл `packagename.txt`. Затем после перезагрузки приложение-функция запускается из пакета и `wwwroot` становится доступным только для чтения. Дополнительные сведения о развертывании из ZIP-файла см. в статье [Непрерывное развертывание Функций Azure из ZIP-файла](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Добавление параметра WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Устранение неполадок

- Запуск из пакета делает `wwwroot` возможным только чтение, поэтому при записи файлов в этот каталог возникнет ошибка.
- Форматы tar и gzip не поддерживаются.
- Эта функция не сопоставлена с локальным кэшем.
- Для повышения производительности холодного запуска используйте параметр local ZIP (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

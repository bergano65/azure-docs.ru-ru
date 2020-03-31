---
title: Выполнить функции Azure из пакета
description: Настройте запуск функций в среде выполнения Функций Azure путем подключения файла пакета развертывания, содержащего файлы проекта приложения-функции.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365277"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Запуск функций Azure из файла пакета

В Azure можно запустить функции непосредственно из файла пакета развертывания в приложении-функции. Другой вариант — развернуть файлы в каталоге `d:\home\site\wwwroot` приложения-функции.

В этой статье описываются преимущества запуска функций из пакета. Здесь также показано, как включить эту функцию в приложении-функции.

> [!IMPORTANT]
> При развертывании функций в приложении функции Linux в [плане Premium](functions-scale.md#premium-plan)вы всегда должны выполнить из файла пакета и [опубликовать приложение с помощью основных инструментов Azure Functions.](functions-run-local.md#project-file-deployment)

## <a name="benefits-of-running-from-a-package-file"></a>Преимущества запуска из файла пакета
  
Запуск функции из файла пакета обеспечивает несколько преимуществ:

+ Снижается риск возникновения проблем из-за блокировки копирования файлов.
+ Можно выполнить развертывание в рабочее приложение (с помощью перезапуска).
+ Обеспечивается надежность файлов, выполняемых в приложении.
+ Повышается производительность [развертываний Azure Resource Manager](functions-infrastructure-as-code.md).
+ Может сократиться время "холодного запуска", особенно для функций JavaScript с большими деревьями пакетов npm.

Для получения дополнительной информации, смотрите [это объявление](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Настройка запуска функций из пакета

Чтобы настроить запуск приложения-функции из пакета, нужно просто добавить `WEBSITE_RUN_FROM_PACKAGE` в параметрах приложения-функции. Параметр `WEBSITE_RUN_FROM_PACKAGE` может иметь одно из следующих значений:

| Значение  | Описание  |
|---------|---------|
| **`1`**  | Рекомендуется для функциональных приложений, работающих на Windows. Запуск из файла пакета в папке `d:\home\data\SitePackages` приложения-функции. Если не [развертывание с развертыванием на молнии,](#integration-with-zip-deployment)эта опция требует, чтобы папка также иметь файл с именем. `packagename.txt` Этот файл содержит только имя файла пакета в папке без каких-либо пробелов. |
|**`<URL>`**  | Расположение определенного файла пакета, который вы хотите запустить. При использовании хранилища BLOB-объектов следует использовать закрытый контейнер с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer), чтобы настроить доступ к пакету в среде выполнения функций. Можно использовать [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи файлов пакета в учетную запись хранения больших двоичных объектов. При указании URL-адреса необходимо также [синхронизировать триггеры](functions-deployment-technologies.md#trigger-syncing) после публикации обновленного пакета. |

> [!CAUTION]
> При запуске приложения функции на Windows, внешний вариант URL дает хуже cold-start производительности. При развертывании приложения функции в `WEBSITE_RUN_FROM_PACKAGE` `1` Windows следует настроиться на развертывание и публикацию.

Далее показано приложение-функция, настроенное для запуска из ZIP-файла, размещенного в хранилище BLOB-объектов Azure:

![Параметр приложения WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакета.

## <a name="integration-with-zip-deployment"></a>Интеграция с помощью развертывания из ZIP-файла

[Развертывание из ZIP-файла][Zip deployment for Azure Functions] — это функция Службы приложений Azure, которая позволяет развертывать проект приложения-функции в каталог `wwwroot`. Проект упакован как ZIP-файл развертывания. Те же API можно использовать для развертывания пакета в папку `d:\home\data\SitePackages`. Если для параметра приложения `WEBSITE_RUN_FROM_PACKAGE` задано значение `1`, API-интерфейсы развертывания из ZIP-файла копируют пакет в папку `d:\home\data\SitePackages`, а не извлекают файлы в `d:\home\site\wwwroot`. Также создается файл `packagename.txt`. После перезагрузки пакет устанавливается `wwwroot` в качестве файловой системы только для чтения. Дополнительные сведения о развертывании из ZIP-файла см. в статье [Непрерывное развертывание Функций Azure из ZIP-файла](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Добавление параметра WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Устранение неполадок

- Run From `wwwroot` Package делает только чтение, так что вы получите ошибку при написании файлов в этом каталоге.
- Форматы Tar и gzip не поддерживаются.
- Эта функция не сочиняется с локальным кэшем.
- Для повышения производительности холодного старта используйте локальный опцион «Зип» (No1).`WEBSITE_RUN_FROM_PACKAGE`
- Run From Package несовместим с опцией`SCM_DO_BUILD_DURING_DEPLOYMENT=true`настройки развертывания ( ), шаг сборки будет проигнорирован во время развертывания.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Непрерывное развертывание для Функций Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

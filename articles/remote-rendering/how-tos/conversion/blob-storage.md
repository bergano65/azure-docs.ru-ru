---
title: Используйте хранилище Azure Blob для преобразования модели
description: Описывает общие шаги по настройке и использованию хранилища кабов для преобразования модели.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681653"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Используйте хранилище Azure Blob для преобразования модели

Служба [преобразования моделей](model-conversion.md) требует доступа к хранилищу Azure blob, чтобы можно было получать входные данные и хранить выходные данные. В этой статье описывается, как сделать наиболее распространенные шаги.

## <a name="prepare-azure-storage-accounts"></a>Подготовка учетных записей хранения Azure

- Создание учетной записи хранилища (StorageV2)
- Создание вхотоза ввода контейнера капли в учетной записи хранения (например, под названием "arrinput")
- Создание контейнера выходной капли в учетной записи хранилища (например, под названием "arroutput")

> [!TIP]
> Для пошаговых инструкций по настройке учетной записи хранилища, взгляните на [кнопку «Быстрый старт: Преобразуйте модель для рендеринга»](../../quickstarts/convert-model.md)

Создание учетной записи хранения и контейнеров с каплями может быть сделано с помощью одного из следующих инструментов:

- [Портал Azure](https://portal.azure.com)
- [az командная линия](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (КЗ, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Убедитесь, что удаленное рендерирование Azure может получить доступ к учетной записи хранилища

Azure Remote Rending необходимо извлекать данные модели из учетной записи хранения и записывать данные обратно в нее.

Вы можете предоставить Azure Remote Rendering доступ к учетной записи хранилища следующим и двумя способами:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Подключите учетную запись хранения Azure с учетной записью удаленного отреживания Azure

Выполните действия, приведенные в разделе [«Создание учетной записи».](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Извлекайs SAS для контейнеров для хранения

Сохраненные подписи доступа (SAS) используются для предоставления доступа к считыванию для ввода и записи доступа для вывода. Мы рекомендуем создавать новые УРИ при каждом преобразовании модели. Поскольку срок действия URIs истекает через некоторое время, сохранение их в течение более длительного времени может привести к неожиданному взлому приложения.

Подробную информацию о SAS можно найти в [документации SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

SAS URI может быть создан с помощью:

- модуль az PowerShell
  - см. [пример скриптов PowerShell](../../samples/powershell-example-scripts.md)
- [az командная линия](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/)
  - нажмите право езду на контейнере "Получить общую подпись доступа" (читай, список доступа для входного контейнера, написать доступ для выходного контейнера)
- SDKs (КЗ, Python ... )

Пример использования общих подписей доступа при преобразовании активов показан в Conversion.ps1 [примеров Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="upload-an-input-model"></a>Загрузить вхотливую модель

Чтобы начать конвертацию модели, необходимо загрузить ее, используя один из следующих вариантов:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - удобный файл управления для загрузки/загрузки/управления файлами на лазурном хранилище капли
- [Командная строка Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [модуль Azure PowerShell;](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - [см. пример скриптов PowerShell](../../samples/powershell-example-scripts.md)
- [Использование хранилища SDK (Python, C . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .](https://docs.microsoft.com/azure/storage/)
- [Использование apIs Rest хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Для примера того, как загрузить данные для преобразования, обратитесь к Conversion.ps1 [из сценариев Примеры Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="get-a-sas-uri-for-the-converted-model"></a>Получите SAS URI для преобразованной модели

Этот шаг аналогичен [извлечению SAS для контейнеров для хранения.](#retrieve-sas-for-the-storage-containers) Однако на этот раз необходимо получить SAS URI для файла модели, который был записан в выходной контейнер.

Например, для получения SAS URI через [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)нажмите правой кнопкой мыши на файл модели и выберите "Получить общую подпись доступа".

Для загрузки моделей необходима общая подпись доступа (SAS), если вы не подключили учетную запись хранилища к учетной записи удаленного рендеринга Azure. Вы можете узнать, как подключить учетную запись в [Создать учетную запись](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка преобразования модели](configure-model-conversion.md)
- [Конверсия модели REST API](conversion-rest-api.md)

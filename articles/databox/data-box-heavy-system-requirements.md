---
title: Большой поле данных Microsoft Azure требования к системе | Документация Майкрософт
description: Дополнительные сведения о программном обеспечении и требования к сети для вашей Azure Data Box большой
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427739"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Поле данных в большой системе требования к Azure (Предварительная версия)

В этой статье описываются важные системные требования для устройства Azure Data Box большой, а также для клиентов, подключающихся к устройству. Мы рекомендуем внимательно ознакомиться с информацией перед развертыванием в большой поле данных и перечитывать ее при необходимости во время развертывания и последующих действий.

В приведенных ниже разделах описаны требования к системе.

* **Требования к программному обеспечению для узлов, подключающихся к большой поле данных** -описание поддерживаемых платформ, обозревателей для локального веб-интерфейса, клиенты SMB и любые дополнительные требования для узлов, которые могут подключаться к полю данных.
* **Требования к сети для интенсивной поле данных** -предоставляет сведения о требования к сети для оптимальной работы большой поле данных устройства.

## <a name="software-requirements"></a>Требования к программному обеспечению

Требования к программному обеспечению включают в себя информацию о поддерживаемых операционных системах, веб-браузерах для локального пользовательского веб-интерфейса и клиентах SMB.

### <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Поддерживаемые файловые системы для клиентов Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Поддерживаемые типы хранилищ

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Поддерживаемые веб-браузеры

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Требования к сети

Центр обработки данных должен иметь высокоскоростную сеть. Самый быстрый тактовых копирования два подключения 40-GbE могут использоваться в параллельном режиме (по одному на узел). Если у вас нет доступных 40-GbE, мы рекомендуем, что имеется по крайней мере два подключения Ethernet 10 Гбит / (по одному на каждый узел).

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание Azure Data Box](data-box-deploy-ordered.md)

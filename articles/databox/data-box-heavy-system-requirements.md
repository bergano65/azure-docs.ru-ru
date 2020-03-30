---
title: Требования к системе Microsoft Azure Data Box Документы Майкрософт
description: Узнайте о требованиях к программному обеспечению и сетевым технологиям для вашего Ящика данных Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260076"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Требования к системе Azure Data Box Heavy

В этой статье описаны важные системные требования к устройству Azure Data Box Heavy и клиентам, подключенным к устройству. Мы рекомендуем вам внимательно просмотреть информацию перед развертыванием Box Heavy, а затем вернуться к ней по мере необходимости во время развертывания и последующей операции.

В приведенных ниже разделах описаны требования к системе.

* **Требования к программному обеспечению для хостов, подключающихся к Data Box Heavy,** описывают поддерживаемые платформы, браузеры для локального веб-интерфейса, клиентов SMB и любые дополнительные требования к хостам, которые могут подключиться к Box Data Box.
* **Требования к сети data Box Heavy** - предоставляет информацию о сетевых требованиях к оптимальной работе устройства Data Box Heavy.

## <a name="software-requirements"></a>Требования к программному обеспечению

Требования к программному обеспечению включают в себя информацию о поддерживаемых операционных системах, веб-браузерах для локального пользовательского веб-интерфейса и клиентах SMB.

### <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Поддерживаемые файловые системы для клиентов Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Поддерживаемые типы хранилища

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Поддерживаемые веб-браузеры

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Требования к сети

Центр обработки данных должен иметь высокоскоростную сеть. Для быстрых скоростей копирования, два 40-GbE соединения могут быть использованы параллельно (по одному на узла). Если у вас нет 40-GbE доступны, мы рекомендуем, чтобы у вас есть по крайней мере два 10-GbE соединения (по одному на узел).

### <a name="port-requirements"></a>Требования к порту

В следующей таблице перечислены порты, которые должны быть открыты в брандмауэре, чтобы обеспечить трафик SMB или NFS. В этой таблице значение *в* или *входящий* относится к направлению, из которого клиент запрашивает доступ к вашему устройству. *Выход* или *выход* относится к направлению, в котором устройство Data Box Heavy отправляет данные за пределы развертывания: например, выход в Интернет.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание Azure Data Box](data-box-deploy-ordered.md)

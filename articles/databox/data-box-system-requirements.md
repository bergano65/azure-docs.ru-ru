---
title: Требования к системе для Microsoft Azure Data Box | Документация Майкрософт
description: Сведения о важных системных требованиях для Azure Data Box и клиентов, подключающихся к Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767876"
---
# <a name="azure-data-box-system-requirements"></a>Требования к системе для Azure Data Box

В этой статье описываются важные требования к системе для Data Box Microsoft Azure и для клиентов, подключающихся к Data Box. Перед развертыванием Data Box рекомендуется внимательно просмотреть сведения, а затем при развертывании и эксплуатации обращаться к ней по мере необходимости.

В приведенных ниже разделах описаны требования к системе.

* **Требования к программному обеспечению:** Для узлов, подключающихся к Data Box, описываются поддерживаемые операционные системы, протоколы обмена файлами, учетные записи хранения, типы хранилищ и браузеры для локального веб-интерфейса.
* **Требования к сети:** В Data Box описаны требования к сетевому подключению и портам для оптимальной работы Data Box.


## <a name="software-requirements"></a>Требования к программному обеспечению

Требования к программному обеспечению включают в себя Поддерживаемые операционные системы, протоколы обмена файлами, учетные записи хранения, типы хранилищ и браузеры для локального веб-интерфейса.

### <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Поддерживаемые протоколы обмена файлами для клиентов

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Подключение к Data Boxным общим ресурсам не поддерживается через ОСТАВШУЮся для экспорта заказов. 

### <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Поддерживаемые типы хранилища

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Поддерживаемые веб-браузеры

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Требования к сети

Центр обработки данных должен иметь высокоскоростную сеть. Настоятельно рекомендуем использовать хотя бы одно соединение Ethernet со скоростью передачи данных 10 Гбит/с. Если 10-гигабитное подключение недоступно, для копирования данных можно использовать канал данных 1-GbE, но это повлияет на скорость копирования.

### <a name="port-requirements"></a>Требования к порту

В следующей таблице перечислены порты, которые необходимо открыть в брандмауэре, чтобы разрешить трафик SMB или NFS. В этой таблице *в* (*входящая*) указывается направление, от которого входящий клиент запрашивает доступ к устройству. *Out* (или *Outbound*) — это направление, в котором устройство Data Box отправляет данные извне, помимо развертывания: например, исходящий трафик в Интернете.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание Azure Data Box](data-box-deploy-ordered.md)

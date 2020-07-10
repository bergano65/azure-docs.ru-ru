---
title: Требования к системе для Microsoft Azure Data Box | Документация Майкрософт
description: Дополнительные сведения о требованиях к программному обеспечению и сети для Microsoft Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 496069ebf64340bc55f03df8dc15304b4888bec0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200293"
---
# <a name="azure-data-box-system-requirements"></a>Требования к системе для Azure Data Box

В этой статье описываются важные требования к системе и рекомендации для Microsoft Azure Data Box и клиентов, подключающихся к Data Box. Прежде чем развертывать Data Box, внимательно ознакомьтесь с приведенной ниже информацией и по мере необходимости возвращайтесь к ней во время развертывания и при последующих действиях.

В приведенных ниже разделах описаны требования к системе.

* **Требования к программному обеспечению для узлов, подключающихся к Data Box**. Описание поддерживаемых платформ, браузеров для локального пользовательского веб-интерфейса, клиентов SMB и любых дополнительных требований для узлов, которые могут подключаться к Data Box.
* **Требования к сети для Data Box**. Информация о требованиях к сети для оптимальной работы Data Box.


## <a name="software-requirements"></a>Требования к программному обеспечению

Требования к программному обеспечению включают в себя информацию о поддерживаемых операционных системах, веб-браузерах для локального пользовательского веб-интерфейса и клиентах SMB.

### <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-filesystems-for-linux-clients"></a>Поддерживаемые файловой системы для клиентов Linux

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

Центр обработки данных должен иметь высокоскоростную сеть. Настоятельно рекомендуем использовать хотя бы одно соединение Ethernet со скоростью передачи данных 10 Гбит/с. Если подключение Ethernet 10 Гбит/с недоступно, для копирования можно использовать канал передачи данных со скоростью 1 Гбит/с, но это повлияет на скорость копирования.

### <a name="port-requirements"></a>Требования к порту

В следующей таблице перечислены порты, которые необходимо открыть в брандмауэре, чтобы разрешить трафик SMB или NFS. В этой таблице значение *в* или *входящий* относится к направлению, из которого клиент запрашивает доступ к вашему устройству. *Out* или *Outbound* — это направление, в котором устройство Data Box отправляет данные извне, помимо развертывания: например, исходящий трафик в Интернете.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание Azure Data Box](data-box-deploy-ordered.md)

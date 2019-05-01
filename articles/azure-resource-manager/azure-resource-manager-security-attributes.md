---
title: Общие атрибуты безопасности для Azure Resource Manager
description: Контрольный список Общие атрибуты безопасности для оценки Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: ebc39dcd9fe921c794add48cc677a799841cbb78
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943625"
---
# <a name="common-security-attributes-for-azure-resource-manager"></a>Общие атрибуты безопасности для Azure Resource Manager

Средства безопасности интегрированы в каждом аспекте службы Azure. В этой статье описываются наиболее распространенные атрибуты безопасности, встроенные в Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>| Yes |  |
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>Шифрование в виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Yes | HTTPS/TLS. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Н/Д | Azure Resource Manager хранит содержимое не клиента, только данные элемента управления. |
| Шифрование на уровне столбцов (службы данных Azure)| Yes | |
| Вызовы API в зашифрованном виде| Yes | |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Нет  | |
| Внедрение поддержки виртуальной сети| Yes | |
| Сетевая изоляция и множество поддержки| Нет  |  |
| Принудительного туннелирования поддержки| Нет  |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Нет  | |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Authentication| Yes | [Azure Active Directory](/azure/active-directory) зависимости.|
| Авторизация| Yes | |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Yes | Журналы действий предоставляют все операции записи (PUT, POST, DELETE) выполняемые с ресурсами; см. в разделе [Просмотр журналов действий для аудита действий с ресурсами](resource-group-audit.md). |
| Ведение журнала данных и аудита| Н/Д | |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Yes |  |

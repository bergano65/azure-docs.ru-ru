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
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002262"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Атрибуты безопасности для Azure Resource Manager

В этой статье описываются атрибуты безопасности, встроенные в Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>| Да |  |
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>При шифровании виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | HTTPS/TLS. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Н/Д | Azure Resource Manager хранит содержимое не клиента, только данные элемента управления. |
| Шифрование на уровне столбцов (службы данных Azure)| Да | |
| Вызовы API в зашифрованном виде| Да | |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Нет | |
| Внедрение поддержки виртуальной сети| Да | |
| Сетевая изоляция и множество поддержки| Нет |  |
| Принудительного туннелирования поддержки| Нет |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Нет | |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Authentication| Да | [Azure Active Directory](/azure/active-directory) зависимости.|
| Авторизация| Да | |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да | Журналы действий предоставляют все операции записи (PUT, POST, DELETE) выполняемые с ресурсами; см. в разделе [Просмотр журналов действий для аудита действий с ресурсами](resource-group-audit.md). |
| Ведение журнала данных и аудита| Н/Д | |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Да |  |

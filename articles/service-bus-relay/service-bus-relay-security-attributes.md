---
title: Общие атрибуты безопасности для ретранслятора служебной шины Azure
description: Контрольный список Общие атрибуты безопасности для оценки ретранслятора служебной шины Azure
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000151"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Атрибуты безопасности для ретранслятора служебной шины Azure

В этой статье описываются атрибуты безопасности, встроенные в ретранслятора служебной шины Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>|  Н/Д | Ретранслятор веб-сокет и не сохраняет данные. |
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>При шифровании виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | Службе требуется TLS. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Нет | Использует только сертификаты Microsoft TLS.  |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | ПРОТОКОЛ HTTPS. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Нет |  |
| Сетевая изоляция и множество поддержки| Нет |  |
| Принудительного туннелирования поддержки| Н/Д | Ретранслятор является TLS-туннеля  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Да | |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Authentication| Да | С помощью SAS. |
| Авторизация|  Да | С помощью SAS. |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Ведение журнала данных и аудита| Да | Успешность подключения / сбоев и ошибок и журнал.  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml).|

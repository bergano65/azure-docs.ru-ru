---
title: Рекомендации по обеспечению безопасности в Azure Digital двойников | Документация Майкрософт
description: Дополнительные сведения о рекомендациях по обеспечению безопасности для цифровых двойников Azure и "Интернет вещей".
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: f5c0f6d9f6f7f35722f3df5f35dc1da945f21b9a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229041"
---
# <a name="azure-digital-twins-security-best-practices"></a>Рекомендации по обеспечению безопасности Azure Digital Twins

Служба безопасности Azure Digital Twins предоставляет детализированное управление доступом к определенным ресурсам и действиям в графе Интернета вещей. Эти функции предоставляются с помощью детализированных возможностей управления ролями и доступом, которые называются [управление доступом на основе ролей](./security-role-based-access-control.md).

Azure Digital Twins также использует другие функции безопасности Интернета вещей Azure, включая Azure Active Directory (Azure AD). Поэтому при настройке и обеспечении безопасности приложений, созданных с помощью Azure Digital Twins, необходимо использовать множество одинаковых [рекомендаций по обеспечению безопасности "Интернета вещей" (IoT)](../iot-fundamentals/iot-security-best-practices.md), применяемых сейчас.

В этой статье кратко излагаются основные рекомендации, которые следует соблюдать.

> [!IMPORTANT]
> Ознакомьтесь с дополнительными ресурсами безопасности, чтобы обеспечить максимальную безопасность пространства Интернета вещей. Обязательно укажите поставщиков устройств.

> [!TIP]
> Использование [центра безопасности Azure для Интернета вещей](https://docs.microsoft.com/azure/asc-for-iot/) для обнаружения угроз и уязвимостей безопасности IOT.

## <a name="iot-security-best-practices"></a>Рекомендации по обеспечению безопасности Интернета вещей

Далее представлены некоторые ключевые методы защиты устройств Интернета вещей.

> [!div class="checklist"]
> * Обеспечьте защиту каждого устройства, подключенного к пространству Интернета вещей, от несанкционированного доступа.
> * Ограничьте роль каждого устройства, датчика и пользователя в пространстве Интернета вещей. При компрометации влияние сводится к минимуму.
> * Рассмотрите возможность использования фильтрации IP-адресов и ограничения портов устройств.
> * Ограничьте операции ввода-вывода и пропускную способность устройства для повышения производительности. Ограничение скорости может повысить безопасность за счет предотвращения атак типа "отказ в обслуживании".
> * Обновляйте встроенное по устройства, операционную систему и программное обеспечение в актуальном состоянии.
> * Периодически проверяйте и просматривайте рекомендации по обеспечению безопасности устройств, программного обеспечения, сети и шлюза, так как они продолжают совершенствоваться и развиваться.

Далее представлены некоторые ключевые методы защиты пространства Интернета вещей.

> [!div class="checklist"]
> * Шифруйте сохраненные, хранящиеся или постоянные данные.
> * Требуйте периодической смены или обновления паролей или ключей.
> * Тщательно ограничивайте доступ и разрешения с помощью ролей. См. раздел [Рекомендации по управлению доступом на основе ролей](#role-based-access-control-best-practices), приведенный ниже.
> * Рассмотрим разделенную топологию сети, чтобы устройства в каждой сети были изолированы от других.
> * Используйте эффективную систему шифрования Требовать длинные пароли, использовать безопасные протоколы и [многофакторную проверку подлинности](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Отслеживайте](./how-to-configure-monitoring.md) ресурсы Интернета вещей, чтобы наблюдать за выбросами, угрозами или параметрами ресурсов, которые выходят за рамки обычных операций. Используйте Azure Analytics для управления мониторингом.

> [!IMPORTANT]
> Ознакомьтесь с [рекомендациями по обеспечению безопасности Azure IOT](../iot-fundamentals/iot-security-best-practices.md) , чтобы начать комплексную стратегию безопасности IOT.

> [!NOTE]
> Дополнительные сведения об обработке и мониторинге событий см. в разделе [Маршрутизация событий и сообщений](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Рекомендации по использованию Azure Active Directory

Azure Digital двойников использует [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) для проверки подлинности пользователей и защиты приложений. Служба Azure Active Directory поддерживает проверку подлинности для различных современных архитектур. Все они основаны на стандартных отраслевых протоколах OAuth 2.0 или OpenID Connect. Далее приведены несколько основных методов защиты пространства Интернета вещей для Azure Active Directory:

> [!div class="checklist"]
> * Храните секреты и ключи приложения Azure Active Directory в безопасном месте, например в [Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Для проверки подлинности используйте сертификат, выданный доверенным [центром сертификации](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md), а не секреты приложения.
> * Ограничьте область доступа OAuth 2.0 для токена.
> * Проверьте срок действия токена и остается ли токен действительным.
> * Установите соответствующие сроки действия токенов.
> * Обновите токены с истекшим сроком действия.

## <a name="role-based-access-control-best-practices"></a>Рекомендации по управлению доступом на основе ролей

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Дополнительная информация

* Рекомендации по Интернету вещей Azure см. в [этой статье](../iot-fundamentals/iot-security-best-practices.md).

* Дополнительные сведения об управлении доступом на основе ролей см. в статье [Контроль доступа на основе ролей](./security-role-based-access-control.md).

* Дополнительные сведения о проверке подлинности см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).
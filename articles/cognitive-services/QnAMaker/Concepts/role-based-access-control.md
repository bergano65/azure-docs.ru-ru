---
title: Совместная работа с другими пользователями — QnA Maker
description: ''
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: c3d6e21b45bccbdaeeee350bac79be680783eb24
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147521"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Совместная работа с другими авторами и редакторами

Работайте совместно с другими авторами и редакторами с помощью управления доступом на основе ролей Azure (Azure RBAC), размещенного на QnA Maker ресурсе.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>Доступ предоставляется на QnA Maker ресурсе

Все разрешения контролируются разрешениями, размещенными в ресурсе QnA Maker. Эти разрешения согласованы с доступом для чтения, записи, публикации и полного доступа.

Эта функция Azure RBAC включает в себя следующее:
* Azure Active Directory (AAD) обеспечивает обратную совместимость 100% с проверкой подлинности на основе ключей для владельцев и участников. Клиенты могут использовать проверку подлинности на основе ключей или проверку подлинности на основе Azure RBAC в своих запросах.
* Быстро добавляйте авторов и редакторы ко всем базам знаний в ресурсе, так как управление осуществляется на уровне ресурсов, а не на уровне базы знаний.

## <a name="access-is-provided-by-a-defined-role"></a>Доступ предоставляется определенной ролью.

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Поток проверки подлинности

На следующей схеме показан поток с точки зрения автора для входа на портал QnA Maker и использования API-интерфейсов разработки.

> [!div class="mx-imgBorder"]
> ![На следующей схеме показан поток с точки зрения автора для входа на портал QnA Maker и использования API-интерфейсов разработки.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Шаги|Описание|
|--|--|
|1|Портал получает маркер для ресурса QnA Maker.|
|2|Портал вызывает соответствующий API QnA Maker создания (APIM), передавая маркер вместо ключей.|
|3|API службы QnA Maker проверяет маркер.|
|4 |API службы QnA Maker вызывает службу QnAMaker.|

Если вы планируете вызывать [API-интерфейсы создания](../How-To/collaborate-knowledge-base.md), см. Дополнительные сведения о настройке проверки подлинности.

## <a name="authenticate-by-qna-maker-portal"></a>Проверка подлинности с помощью портала QnA Maker

При создании и совместной работе с помощью QnA Maker портала после [добавления соответствующей роли в ресурс для совместной работы](../How-To/collaborate-knowledge-base.md)портал QnA Maker управляет всеми разрешениями на доступ.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Проверка подлинности с помощью QnA Maker API и пакетов SDK

При создании и совместной работе с использованием API-интерфейсов с помощью функций RESTFUL или пакетов SDK необходимо [создать субъект-службу](../../authentication.md#assign-a-role-to-a-service-principal) для управления проверкой подлинности.

## <a name="next-step"></a>Следующий шаг

* Разработка базы знаний для [языков](design-language-culture.md) и [клиентских приложений](integration-with-other-applications.md)

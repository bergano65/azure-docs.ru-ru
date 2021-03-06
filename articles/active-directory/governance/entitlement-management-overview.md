---
title: Общие сведения об управлении правами — Azure AD
description: Ознакомьтесь с обзором Azure Active Directory управления назначением и способов его использования для управления доступом к группам, приложениям и сайтам SharePoint Online для внутренних и внешних пользователей.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a99b9089e568351cf736310e778ba477441407
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422579"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Что собой представляет управление правами Azure AD?

Управление обслуживанием Azure Active Directory (Azure AD) — это функция управления [удостоверениями](identity-governance-overview.md) , которая позволяет организациям управлять жизненным циклом удостоверений и доступа в масштабе, автоматизируя рабочие процессы запросов доступа, назначения доступа, проверки и истечения срока действия.

Сотрудникам в организациях требуется доступ к различным группам, приложениям и сайтам для выполнения своих задач. Управлять этим доступом сложно, так как изменение требований — добавляются новые приложения или пользователям требуются дополнительные права доступа.  Этот сценарий становится более сложным при совместной работе с внешними организациями. Вы, возможно, не узнаете, кто из других организаций должен иметь доступ к ресурсам Организации, и не узнает, какие приложения, группы или сайты использует ваша организация.

Управление назначением Azure AD позволяет более эффективно управлять доступом к группам, приложениям и сайтам SharePoint Online для внутренних пользователей, а также пользователям за пределами Организации, которым необходим доступ к этим ресурсам.

## <a name="why-use-entitlement-management"></a>Зачем использовать управление назначениями?

Корпоративные организации часто сталкиваются с проблемами при управлении доступом сотрудников к ресурсам, таким как:

- Пользователи могут не иметь знания о том, какой доступ они должны иметь, и даже при их наличии у них может возникнуть трудности с поиском нужных лиц для утверждения доступа.
- Когда пользователи находят и получают доступ к ресурсу, они могут работать дольше, чем требуется для бизнес-целей.

Эти проблемы сложны для пользователей, которым необходим доступ из другой организации, например от внешних пользователей, которые относятся к организациям цепочки поставок или другим деловым партнерам. Пример.

- Никто из пользователей не может быть уверен в том, что все конкретные пользователи в каталогах других организаций могут пригласить их.
- Даже если бы они могли пригласить этих пользователей, никто в этой Организации не может забывать управлять всем доступом пользователей постоянно.

Управление назначениями Azure AD может помочь решить эти проблемы.  Чтобы узнать больше о том, как клиенты используют управление назначением Azure AD, вы можете ознакомиться с примером [использования в Avanade](https://aka.ms/AvanadeELMCase) и [практическом примере](https://aka.ms/CentricaELMCase).  В этом видео представлен обзор управления назначением и его ценности.

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Что можно сделать с помощью управления назначениями?

Ниже приведены некоторые возможности управления назначениями.

- Делегирование Неадминистраторам возможности создания пакетов доступа. Эти пакеты доступа содержат ресурсы, которые пользователи могут запрашивать, а диспетчеры делегированных пакетов доступа могут определять политики с правилами, которые пользователи могут запрашивать, кто должен утверждать их доступ и когда срок действия доступа истечет.
- Выберите подключенные Организации, пользователи которых могут запрашивать доступ.  Когда пользователь, который еще не находится в каталоге, запрашивает доступ и утверждается, он автоматически приглашен в каталог и назначил доступ.  Когда срок действия доступа истечет, учетная запись B2B в каталоге может быть автоматически удалена, если у них нет других назначений пакетов доступа.

Вы можете приступить к работе с нашим [руководством, чтобы создать пакет первого доступа](entitlement-management-access-package-first.md). Вы также можете ознакомиться с [распространенными сценариями](entitlement-management-scenarios.md)или смотреть видео, в том числе

- [Развертывание управления назначением Azure AD в Организации](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Мониторинг и масштабирование использования управления назначением Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Делегирование в управлении назначением](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Что такое пакеты доступа и какие ресурсы можно управлять с их помощью?

Управление назначением предоставляет Azure AD понятие *пакета Access*. Пакет Access — это набор всех ресурсов с доступом, которым пользователь должен работать над проектом или выполнять свои задачи. Пакеты доступа используются для контроля доступа внутренних сотрудников, а также пользователей за пределами Организации.

 Ниже приведены типы ресурсов, которые можно использовать для управления доступом пользователей к с помощью управления назначением.

- Членство в группах безопасности Azure AD
- Членство в группах и командах Office 365
- Назначение корпоративных приложений Azure AD, включая приложения SaaS и пользовательские интегрированные приложения, поддерживающие Федерацию, единый вход и (или) подготовку
- Членство сайтов SharePoint Online

Вы также можете контролировать доступ к другим ресурсам, которые используют группы безопасности Azure AD или группы Office 365.  Пример.

- Вы можете предоставить лицензии пользователям для Microsoft Office 365 с помощью группы безопасности Azure AD в пакете Access и настройки [лицензирования на основе групп](../users-groups-roles/licensing-groups-assign.md) для этой группы.
- Вы можете предоставить пользователям доступ для управления ресурсами Azure с помощью группы безопасности Azure AD в пакете Access и создания [назначения роли Azure](../../role-based-access-control/role-assignments-portal.md) для этой группы.

## <a name="how-do-i-control-who-gets-access"></a>Разделы справки, кто получает доступ?

С помощью пакета доступа администратор или диспетчер пакетов с делегированным доступом Перечисляет ресурсы (группы, приложения и сайты) и роли, необходимые пользователям для этих ресурсов.

Пакеты доступа также включают одну или несколько *политик*. Политика определяет правила или снятие для назначения доступа к пакету. Каждая политика может использоваться, чтобы гарантировать, что только соответствующие пользователи смогут запрашивать доступ, что есть утверждающие запросы, и что их доступ к этим ресурсам ограничен по времени, и срок их действия истечет, если он не продлен.

![Доступ к пакету и политикам](./media/entitlement-management-overview/elm-overview-access-package.png)

В рамках каждой политики администратор или диспетчер пакетов Access определяет

- Уже существующие пользователи (обычно сотрудники или уже приглашенные гости) или партнерские организации внешних пользователей, которые могут запрашивать доступ.
- Процесс утверждения и пользователи, которые могут утверждать или запрещать доступ
- Продолжительность назначения доступа пользователя после утверждения до истечения срока действия назначения

На следующей диаграмме показан пример различных элементов управления назначением. В нем показан один каталог с двумя примерами пакетов доступа.

- **Доступ к пакету 1** включает в себя одну группу как ресурс. Доступ определяется с помощью политики, которая позволяет набору пользователей в каталоге запрашивать доступ.
- **Доступ к пакету 2** включает в себя группу, приложение и сайт SharePoint Online в качестве ресурсов. Доступ определяется двумя разными политиками. Первая политика позволяет набору пользователей в каталоге запрашивать доступ. Вторая политика позволяет пользователям во внешнем каталоге запрашивать доступ.

![Обзор управления назначениями](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Когда следует использовать пакеты Access?

Пакеты доступа не заменяют другие механизмы назначения доступа.  Они наиболее подходят в следующих ситуациях:

- Сотрудникам требуется ограниченное время доступа для конкретной задачи.  Например, можно использовать лицензирование на основе групп и динамическую группу, чтобы все сотрудники имели почтовый ящик Exchange Online, а затем использовать пакеты Access для ситуаций, в которых сотрудникам требуется дополнительный доступ, например для чтения ресурсов отделов из другого. Название.
- Доступ должен быть утвержден руководителем сотрудника или другими назначенными лицами.
- Отделы хотят управлять собственными политиками доступа для своих ресурсов без участия в ИТ.  
- Две или более организации совместно работают над проектом, и в результате для доступа к ресурсам другой организации несколько пользователей из одной организации необходимо будет использовать через Azure AD B2B.

## <a name="how-do-i-delegate-access"></a>Разделы справки делегирование доступа?

 Пакеты для доступа добавляются в контейнеры, называемые *каталогами*.  У вас может быть один каталог для всех пакетов доступа, или можно назначить отдельных пользователей для создания собственных каталогов. Администратор может добавлять ресурсы в любой каталог, но не администратор может добавить в каталог ресурсы, которыми они владеют. Владелец каталога может добавлять других пользователей в качестве совладельцев в каталог или в качестве доступа к диспетчерам пакетов.  Эти сценарии описаны далее в статье [Делегирование и роли в управлении](entitlement-management-delegate.md)назначением Azure AD.

## <a name="summary-of-terminology"></a>Общие сведения о терминологии

Чтобы лучше понять управление назначением и документацию, вы можете вернуться к следующему списку терминов.

| Срок действия | Description |
| --- | --- |
| доступ к пакету | Набор ресурсов, которые требуются для команды или проекта и управляются политиками. Пакет Access всегда содержится в каталоге. Необходимо создать новый пакет Access для сценария, в котором пользователям необходимо запросить доступ.  |
| запрос на доступ | Запрос на доступ к ресурсам в пакете Access. Обычно запрос проходит через рабочий процесс утверждения.  Если утверждение утверждено, запрашивающий пользователь получает назначение пакета доступа. |
| присваивание | Назначение пакета доступа пользователю гарантирует, что у пользователя все роли ресурсов этого пакета доступа.  Доступ к назначениям пакетов обычно ограничен по времени до истечения срока их действия. |
| catalog | Контейнер связанных ресурсов и пакетов доступа.  Каталоги используются для делегирования, чтобы не администраторы могли создавать собственные пакеты доступа. Владельцы каталога могут добавлять ресурсы, которыми они владеют, в каталог. |
| Создатель каталога | Коллекция пользователей, которым разрешено создавать новые каталоги.  Если пользователь без прав администратора, который является автором каталога, создает новый каталог, он автоматически становится владельцем этого каталога. |
| подключенная Организация | Внешний каталог или домен Azure AD, с которым связана связь. Пользователи из подключенной организации могут быть указаны в политике в качестве разрешенного запроса доступа. |
| policy | Набор правил, определяющий жизненный цикл доступа, например способ получения доступа пользователями, кто может утверждать и как долго пользователи получают доступ с помощью назначения. Политика связана с пакетом Access. Например, пакет Access может иметь две политики: один для сотрудников запрашивает доступ, а второй — для запроса доступа внешним пользователям. |
| ресурс | Ресурс-контейнер, например группа Office, группа безопасности, приложение или сайт SharePoint Online с ролью, которой может быть предоставлен доступ пользователю. |
| Каталог ресурсов | Каталог, содержащий один или несколько ресурсов для совместного использования. |
| роль ресурса | Коллекция разрешений, связанных с и определенным ресурсом. Группа имеет две роли — "член" и "владелец". Сайты SharePoint обычно имеют три роли, но могут иметь дополнительные пользовательские роли. Приложения могут иметь пользовательские роли. |


## <a name="license-requirements"></a>Требования лицензий

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Специализированные облака, такие как Azure для государственных организаций, Azure для Германии и Azure для Китая, в настоящее время недоступны для использования.

### <a name="which-users-must-have-licenses"></a>У каких пользователей должны быть лицензии?

У клиента должно быть по крайней мере столько лицензий Azure AD Premium P2, которые являются активными пользователями в управлении назначениями. Пользователи активных участников в управлении назначениями включают:

- Пользователь, инициирующий или утверждающий запрос пакета доступа.
- Пользователь, которому был назначен пакет Access.
- Пользователь, Управляющий пакетами доступа.

В рамках лицензий для пользователей-членов можно также разрешить несколько гостевых пользователей взаимодействовать с управлением назначением. Сведения о том, как вычислить количество гостевых пользователей, которые можно включить, см. в разделе [Azure Active Directory рекомендации по лицензированию службы совместной работы B2B](../b2b/licensing-guidance.md).

Сведения о назначении лицензий пользователям см. [в статье назначение или удаление лицензий с помощью портала Azure Active Directory](../fundamentals/license-users-groups.md). Обратите внимание, что сейчас управление назначением не обеспечивает принудительное назначение лицензий пользователям.

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство. Создание пакета первого доступа](entitlement-management-access-package-first.md)
- [Распространенные сценарии](entitlement-management-scenarios.md)

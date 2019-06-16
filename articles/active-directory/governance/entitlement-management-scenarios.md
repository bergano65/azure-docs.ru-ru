---
title: Распространенные сценарии, в управление правами Azure AD (Предварительная версия) — Azure Active Directory
description: Узнайте основные действия, которые необходимо выполнить для распространенных сценариев в управление правами Azure Active Directory (Предварительная версия).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473135"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Распространенные сценарии, в управление правами Azure AD (Предварительная версия)

> [!IMPORTANT]
> Управление правами Azure Active Directory (Azure AD) сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Существует несколько способов, можно настроить управление правами для вашей организации. Тем не менее если вы только начинаете работу, полезно понять распространенные сценарии для администраторов, утверждающих лиц и запрашивающими сторонами.

## <a name="administrators"></a>Администраторы

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Я впервые управление правами, и мне нужна помощь по началу работы

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | [Следуйте указаниям руководства, чтобы создать свой первый пакет доступа](entitlement-management-access-package-first.md) | [![Значок Azure портала](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Я хочу разрешить пользователям в каталоге, чтобы запросить доступ для группы приложений и сайтов SharePoint

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Создание нового пакета доступа в каталоге](entitlement-management-access-package-create.md#start-new-access-package) | ![Создание пакета для доступа](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Добавление ролей ресурсов, доступ к пакету](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Группы</li><li>ПРИЛОЖЕНИЯ</li><li>Сайты SharePoint</li></ul> | ![Добавление ролей ресурсов](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Добавление политики](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Для пользователей в каталоге</li><li>Требуется утверждение</li><li>Параметры срока действия</li></ul> | ![добавление политики;](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Я хочу разрешить пользователям из моего бизнеса партнеров каталога, (в том числе пользователей еще не в моем каталоге), чтобы запросить доступ для группы приложений и сайтов SharePoint

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Создание нового пакета доступа в каталоге](entitlement-management-access-package-create.md#start-new-access-package) | ![Создание пакета для доступа](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Добавление ролей ресурсов, доступ к пакету](entitlement-management-access-package-edit.md#add-resource-roles) | ![Добавление ролей ресурсов](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Добавление политики для внешних пользователей](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Для пользователей, не в каталоге</li><li>Требуется утверждение</li><li>Параметры срока действия</li></ul> | ![Добавление политики для внешних пользователей](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Отправьте ссылку портала мой доступ для запроса доступа пакета для деловых партнеров](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Деловой партнер может предоставить ссылку на своих пользователей</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Изменение групп, приложений или сайтов SharePoint в пакет доступа

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** Откройте пакет доступа | ![Добавление ролей ресурсов](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Добавление или удаление ролей ресурсов](entitlement-management-access-package-edit.md#add-resource-roles) | ![Добавление ролей ресурсов](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Я хочу просмотреть, кто имеет назначения для группы приложений и сайтов SharePoint

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** Открыть пакет доступа | ![Добавление ролей ресурсов](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Просмотр назначений](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Представление, какие пользователи имеют доступ к пакету доступ</li><li>Представление, истек срок действия доступа какого пользователя</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Я хочу просмотреть группы, приложения или сайты SharePoint, которые пользователь имеет доступ к

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | [Просмотреть отчет назначения пользователей](entitlement-management-reports.md)<ul><li>Представление, когда в каком они запрашиваются и кто утвердил</li></ul> |  |

## <a name="approvers"></a>Утверждающих

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Я хочу утверждать запросы на доступ к группам, приложений или сайтов SharePoint

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Запрос на открытие на портале мой доступ](entitlement-management-request-approve.md#open-request) | [![Мои портала значок доступа](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Утвердить запрос на доступ](entitlement-management-request-approve.md#approve-or-deny-request) | ![Утвердить доступ](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Запрашивающие стороны

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Я хочу просмотреть группы, приложения или сайты SharePoint, доступные для меня и запросить доступ

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Войдите на портал My доступа](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Мои портала значок доступа](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Найдите пакет доступа |  |
> | **3.** [Запрос доступа](entitlement-management-request-access.md#request-an-access-package) | ![Запрос доступа](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Я внешнего пользователя и хочу запросить доступ для группы приложений и сайтов SharePoint с прямой ссылкой

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Найти Мои доступа портала ссылка, которую вы получили](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Войдите на портал My доступа](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Мои портала значок доступа](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Запрос доступа](entitlement-management-request-access.md#request-an-access-package) | ![Запрос доступа внешних пользователей](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Я хочу просмотреть группы, приложений или сайтов SharePoint, которые я уже есть доступ к

> [!div class="mx-tableFixed"]
> | Действия | Пример |
> | --- | --- |
> | **1.** [Войдите на портал My доступа](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Мои портала значок доступа](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Просмотр пакетов active доступа |  |

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Создайте свой первый пакет доступа](entitlement-management-access-package-first.md)
- [Изменение и администрирование существующего пакета для доступа](entitlement-management-access-package-edit.md)

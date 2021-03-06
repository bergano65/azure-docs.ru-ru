---
title: Что такое Azure AD Connect подготовки облака. | Документация Майкрософт
description: Описывает Azure AD Connect подготовки облака.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10c778025c3def384f621cb5311d0d71ccb457d7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951128"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Что такое подготовка облака Azure AD Connect?
Azure AD Connect подготовка облачных облаков — это новый агент Майкрософт, предназначенный для удовлетворения и выполнения целей гибридной идентификации для синхронизации пользователей, групп и контактов с Azure AD.  Его можно использовать вместе с Azure AD Connect Sync и предоставляет следующие преимущества:
    
- Поддержка синхронизации с клиентом Azure AD из нескольких лесов, отключенных Active Directory среде леса. типичные сценарии включают слияние & приобретения, когда приобретенные леса AD изолированы от AD родительской компании. леса и компании, у которых исторически несколько лесов AD.
- Упрощенная установка с помощью неплотных агентов подготовки: агенты выполняют роль моста из AD в Azure AD, а все настройки синхронизации управляются в облаке. 
- Для упрощения развертывания с высоким уровнем доступности можно использовать несколько агентов подготовки, особенно важных для организаций, которые полагаются на синхронизацию хэшей паролей из AD в Azure AD.


![Что такое Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Как Azure AD Connect облачная подготовка отличается от Azure AD Connect синхронизации?
Подготовка облачных служб к работе с Azure AD с помощью Azure AD Connect подготовки в облаке осуществляется в Microsoft Online Services. Организациям требуется только развертывание, в локальной среде и в размещенной в IaaS средах — это упрощенный агент, который выступает в качестве моста между Azure AD и AD. Конфигурация подготовки хранится в Azure AD и управляется как часть службы.

В следующей таблице приведено сравнение Azure AD Connect и Azure AD Connect подготовки облака.

| Компонент | Синхронизация Azure Active Directory Connect| Azure Active Directory Connect подготовки облака |
|:--- |:---:|:---:|
|Подключение к одному локальному лесу AD|● |● |
| Подключение к нескольким локальным лесам AD |● |● |
| Подключение к нескольким отключенным локальным лесам AD | |● |
| Модель установки упрощенного агента | |● |
| Несколько активных агентов для обеспечения высокой доступности | |● |
| Подключение к каталогам LDAP|●| | 
| Поддержка пользовательских объектов |● |● |
| Поддержка объектов групп |● |● |
| Поддержка объектов Contact |● |● |
| Поддержка объектов устройств |● | |
| Разрешить базовую настройку потоков атрибутов |● |● |
| Синхронизация определяемых клиентом атрибутов AD (расширения каталогов) |● | |
| Поддержка синхронизации хэшей паролей |●|●|
| Поддержка сквозной проверки подлинности |●||
| Поддержка Федерации |●|●|
| Эффективный единый вход|● |●|
| Поддержка установки на контроллере домена |● |● |
| Поддержка Windows Server 2012 и Windows Server 2012 R2 |● |● |
| Фильтрация по доменам, подразделениям и группам |● |● |
| Фильтрация по значениям атрибутов объектов |● | |
| Разрешение синхронизации минимального набора атрибутов (MinSync) |● |● |
| Разрешение удаления атрибутов из потока из AD в Azure AD |● |● |
| Разрешение дополнительной настройки потоков атрибутов |● | |
| Поддержка обратной записи (пароли, устройства, группы) |● | |
| Поддержка доменных служб Azure AD|● | |
| Гибридная конфигурация Exchange |● | |

## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Установка подготовки облака](how-to-install.md)

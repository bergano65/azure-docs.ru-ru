---
title: Понимание Azure AD Подключите 1.4.xx.x и исчезновение устройства Документы Майкрософт
description: В этом документе описывается проблема, возникающая с версией 1.4.xx.x Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176023"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Понимание Azure AD Подключите 1.4.xx.x и исчезновение устройства
С версией 1.4.xx.x Azure AD Connect некоторые клиенты могут увидеть, как некоторые или все их устройства Windows исчезают из Azure AD. Это не вызывает беспокойства, так как эти идентификаторы устройств не используются Azure AD во время авторизации условия. Это изменение не удалит устройства Windows, которые были правильно зарегистрированы в Azure AD для гибридного соединения Azure AD.

Если в Azure AD удаление объектов устройства превышает порог удаления экспорта, клиенту рекомендуется разрешить удаление. [Как: разрешить удаляет поток, когда они превышают порог удаления](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Историческая справка
Устройства Windows, зарегистрированные как Hybrid Azure AD Joined, представлены в Azure AD в качестве объектов устройств. Эти объекты устройства могут быть использованы для условного доступа. Устройства Windows 10 синхронизируются с облаком через Azure AD Connect, устройства Windows нисходящего уровня регистрируются непосредственно с помощью AD FS или бесшовных одного входного.

## <a name="windows-10-devices"></a>Устройства под управлением Windows 10
Только устройства Windows 10 с определенным значением атрибута userCertificate, настроенным на гибридную Azure AD Join, должны быть синхронизированы с облаком с помощью Azure AD Connect. В предыдущих версиях Azure AD Connect это требование не соблюдалось строго, что приводилось к ненужным объектам устройств в Azure AD. Такие устройства в Azure AD всегда находились в «ожидающем» состоянии, поскольку эти устройства не предназначались для регистрации в Azure AD. 

Эта версия Azure AD Connect будет синхронизировать только устройства Windows 10, которые правильно настроены на гибридное ad Azure. Объекты устройств Windows 10 без присоединения Azure AD к определенному UserCertificate будут удалены из Azure AD.

## <a name="down-level-windows-devices"></a>Устройства Windows низкого уровня
Подключение Azure AD Никогда не должно синхронизировать [устройства Windows низкого уровня.](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices) Любые устройства в Azure AD, ранее синхронизированные неправильно, теперь будут удалены из Azure AD. Если Azure AD Connect пытается удалить [устройства Windows низкого уровня,](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)то устройство не является тем, что было создано [Microsoft Workplace Join для компьютеров MSI без Windows 10,](https://www.microsoft.com/download/details.aspx?id=53554) и оно не может быть использовано любой другой функцией Azure AD.

Некоторым клиентам может потребоваться вернуться к вопросу О том, [как: спланировать гибридную реализацию Azure Active Directory,](../devices/hybrid-azuread-join-plan.md) чтобы правильно зарегистрировать свои устройства Windows и убедиться, что такие устройства могут в полной мере участвовать в работе условного доступа на основе устройств. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Как я могу проверить, какие устройства будут удалены с помощью этого обновления?

Чтобы проверить, какие устройства удалены, вы можете использовать этот скрипт PowerShell:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Этот скрипт генерирует отчет о сертификатах, хранящихся в объектах Active Directory Computer, в частности, сертификатах, выданных функцией Hybrid Azure AD.
Он проверяет сертификаты, присутствующие в свойстве UserCertificate объекта в AD, и, для каждого присутствующему сертификата, не истекшего, проверяет, был ли сертификат выдан для функции присоединения Hybrid Azure AD (т.е. имя объекта соответствует CN'S .)
Раньше Azure AD Connect синхронизировал с Azure AD любой компьютер, содержащий по крайней мере один действительный сертификат, но начиная с версии Azure AD Connect 1.4, движок синхронизации может идентифицировать сертификаты гибридного Azure AD и будет «облачным фильтром» компьютерный объект от синхронизации до Azure AD, если нет действительного сертификата гибридного Azure AD.
Устройства Azure AD, которые уже были синхронизированы с AD, но не имеют действительного сертификата соединения Hybrid Azure AD, будут удалены (CloudFiltered-TRUE) с помощью движка синхронизации.

## <a name="next-steps"></a>Next Steps
- [История версии Azure AD Connect](reference-connect-version-history.md)

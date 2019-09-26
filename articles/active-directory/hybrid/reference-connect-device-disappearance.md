---
title: Основные сведения об Azure AD Connect 1.4. XX. x и расходе устройства | Документация Майкрософт
description: В этом документе описывается проблема, возникающая в версии 1.4. XX. x Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316958"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Основные сведения об Azure AD Connect 1.4. XX. x и о выявление устройства
При использовании версии 1.4. XX. x Azure AD Connect некоторые клиенты могут увидеть некоторые или все их устройства с Windows, которые исчезают из Azure AD. Это не является причиной проблемы, так как эти удостоверения устройств не используются Azure AD во время авторизации условного доступа. Это изменение не приведет к удалению устройств Windows, которые были правильно зарегистрированы в Azure AD для гибридного присоединение к Azure AD.

Если вы видите, что удаление объектов устройств в Azure AD превышает пороговое значение удаления экспорта, рекомендуется, чтобы клиент разрешал удаление. [Как разрешить удаление в поток, если они превышают пороговое значение удаления](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Фоновый
Устройства Windows, зарегистрированные как гибридные присоединенные к Azure AD, представлены в Azure AD как объекты устройств. Эти объекты устройств можно использовать для условного доступа. Устройства Windows 10 синхронизируются с облаком с помощью Azure AD Connect, устройства Windows нижнего уровня регистрируются напрямую с помощью AD FS или простого единого входа.

## <a name="windows-10-devices"></a>Устройства с Windows 10
Только устройства Windows 10 с конкретным значением атрибута userCertificate, настроенным с помощью гибридного подключения Azure AD, должны быть синхронизированы с облаком Azure AD Connect. В предыдущих версиях Azure AD Connect это требование не было строго применено, что привело к ненужным объектам устройств в Azure AD. Такие устройства в Azure AD всегда неизменности в состоянии "ожидание", так как эти устройства не предназначены для регистрации в Azure AD. 

Эта версия Azure AD Connect синхронизирует только устройства Windows 10, которые правильно настроены для гибридного присоединения к Azure AD. Объекты устройств Windows 10 без конкретных userCertificate присоединение к Azure AD будут удалены из Azure AD.

## <a name="down-level-windows-devices"></a>Устройства Windows нижнего уровня
Azure AD Connect никогда не должны синхронизировать [устройства Windows нижнего уровня](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Все устройства в Azure AD, которые ранее были синхронизированы неправильно, теперь будут удалены из Azure AD. Если Azure AD Connect пытается удалить [устройства Windows нижнего уровня](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), это значит, что устройство не создано в [Microsoft Workplace Join для MSI-компьютеров, отличных от Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) , и не может использоваться какой-либо другой функцией Azure AD.

Некоторым клиентам может потребоваться перейти [к следующим инструкциям: Спланируйте реализацию](../devices/hybrid-azuread-join-plan.md) гибридного Azure Active Directory JOIN, чтобы правильно зарегистрировать устройства Windows и убедиться, что такие устройства могут полностью участвовать в условном доступе на основе устройств. 

## <a name="next-steps"></a>Следующие шаги
- [Журнал версий Azure AD Connect](reference-connect-version-history.md)

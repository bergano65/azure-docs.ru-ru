---
title: Настройка правила синхронизации в Azure AD Connect | Документация Майкрософт
description: В этой статье приводятся пошаговые инструкции для устранения неполадок при установке Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172439"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Настройка правила синхронизации

## <a name="recommended-steps"></a>**Рекомендуемые действия**

В редакторе правил синхронизации можно изменить старое или создать новое правило синхронизации. Вы должны быть опытным пользователем, чтобы вносить изменения в правила синхронизации. Любые неправильные изменения могут привести к удалению объектов из вашего целевого каталога. Ознакомьтесь с [рекомендованными документами](#recommended-documents), чтобы научиться работать с правилами синхронизации. Чтобы изменить правило синхронизации, выполните следующие действия.

* Запустите редактор синхронизации из меню приложений на рабочем столе, как показано ниже:

    ![Редактор правил синхронизации в списке меню](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Чтобы настроить правило синхронизации по умолчанию, клонируйте существующее правило, нажав кнопку "Изменить" в редакторе правил синхронизации. В результате будет создана и отключена копия стандартного правила по умолчанию. Сохраните клонированное правило с приоритетом менее 100.  Приоритет определяет, какое правило получает преимущество (меньшее числовое значение) при разрешении конфликта, если существует конфликт потока атрибутов.

    ![Редактор правил синхронизации](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* При изменении определенного атрибута в идеале вы должны оставить только измененный атрибут в клонированном правиле.  Затем включите правило по умолчанию, чтобы измененный атрибут поступал из клонированного правила, а другие атрибуты выбирались из стандартного правила по умолчанию. 

* Обратите внимание, что в случае, когда вычисленное значение измененного атрибута равно NULL в клонированном правиле и не равно NULL в стандартном правиле по умолчанию, значение, которое не равно NULL, будет иметь преимущество и заменит значение NULL. Если вы не хотите, чтобы значение NULL заменялось значением, отличным от NULL, присвойте клонированному правилу значение AuthoritativeNull.

* Чтобы изменить правило **для исходящего трафика**, измените фильтр в редакторе правил синхронизации.

## <a name="recommended-documents"></a>**Рекомендуемые документы**
* [Синхронизация Azure AD Connect: технические понятия](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Синхронизация Azure AD Connect: основные сведения об архитектуре](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Синхронизация Azure AD Connect: общие сведения о декларативной подготовке](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Синхронизация Azure AD Connect: общие сведения о выражениях декларативной подготовки](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Синхронизация Azure AD Connect: общие сведения о конфигурации по умолчанию](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Синхронизация Azure AD Connect: общие сведения о пользователях, группах и контактах](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Синхронизация Azure AD Connect: теневые атрибуты](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Дальнейшие действия
- [Синхронизация Azure AD Connect: общие сведения о синхронизации и ее настройка](how-to-connect-sync-whatis.md).
- [Что собой представляет гибридная идентификация](whatis-hybrid-identity.md).

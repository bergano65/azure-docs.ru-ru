---
title: 'Агент по подключению Azure AD Connect: История выпуска версии Документы Майкрософт'
description: В этой статье перечислены все выпуски агента по обеспечению подключения Azure AD и описаны новые функции и исправленные проблемы
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183250"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Агент по подключению Azure AD Connect: история выпуска версии
В этой статье перечислены версии и особенности выпущенного агента по подключению к функциям Azure Active Directory Connect. Команда Azure AD регулярно обновляет агента по обеспечению с новыми функциями и функциональностью. Агент по предоставлению услуг обновляется автоматически при выпуске новой версии. 

Корпорация Майкрософт предоставляет непосредственную поддержку последней версии агента и одной версии раньше.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Состояние выпуска

Декабрь 4, 2019: Выпущен для скачивания

### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения

* Включает поддержку [облачной подготовки Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) для синхронизации данных пользователей, контактов и групп от предприимчивого Active Directory до Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Состояние выпуска

Сентябрь 9, 2019: Выпущен для автоматического обновления

### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения

* Возможность настройки дополнительной трассировки и регистрации для отладки проблем Сапожного агента
* Возможность извлечения только тех атрибутов Azure AD, которые настроены в отображении для повышения производительности синхронизации

### <a name="fixed-issues"></a>Исправленные проблемы

* Исправлена ошибка, в которой агент вошел в состояние без ответа, если возникли проблемы с сбоями подключения Azure AD
* Исправлена ошибка, которая вызвала проблемы при чтении двоичных данных из Active Directory Azure
* Исправлена ошибка, в которой агент не смог возобновить доверие с облачным гибридным сервисом идентификации

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Состояние выпуска

Январь 23, 2019: Выпущен для скачивания

### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения

* Обновлена архитектура агента подготовки и разъема для повышения производительности, стабильности и надежности 
* Упрощена конфигурация агента подготовки с помощью мастера установки, управляемой uI 
* Добавлена поддержка автоматических обновлений агента


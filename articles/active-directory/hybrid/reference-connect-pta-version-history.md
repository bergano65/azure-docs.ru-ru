---
title: 'Azure AD Pass-через аутентификацию: История выпуска версии Документы Майкрософт'
description: В этой статье перечислены все релизы агента azure AD Pass-through Authentication
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75b127f8429650d46af9f171ed7ff03692f1499e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379907"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Агент azure AD Pass-through Authentication: история выпуска версии 
 
Агенты, установленные на территории, позволяющие осуществлять проверку подлинности прохода, регулярно обновляются для предоставления новых возможностей. В этой статье перечислены версии и функции, которые добавляются при введении новой функциональности. Агенты пропускной информации обновляются автоматически при выпуске новой версии. 

Вот связанные темы: 

- [Пользователь входе в систему с Azure AD Pass-through Authentication](how-to-connect-pta.md) 
- [Установка агента Azure AD Pass-through Authentication](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Статус релиза: 
04/09/2020: Выпущен для скачивания

### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения

- Добавлена поддержка таргетинга облачных сред при установке. Пакет можно прикрепить к заданной облачной среде.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Состояние выпуска 
1/22/2019: Выпущен для скачивания  
### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения 
- Добавлена поддержка надежных каналов Service Bus для добавления еще одного уровня устойчивости соединения для исходящих соединений 
- Принудительное соблюдение TLS 1.2 во время регистрации агента 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Состояние выпуска 
4/10/2018: Выпущен для скачивания  
### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения 
- Поддержка подключения к веб-разъему 
- Установите TLS 1.2 в качестве протокола по умолчанию для агента 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Состояние выпуска 
1/31/2018: Выпущен для скачивания  
### <a name="fixed-issues"></a>Исправленные проблемы 

- Исправлена ошибка, которая вызвала некоторые утечки памяти в агенте. 
- Обновлена версия Azure Service Bus, которая включает в себя исправление ошибки для проблем тайм-аута разъема. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Состояние выпуска 
11/26/2017: Выпущен для скачивания  
### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения 
- Добавлена поддержка подключений на основе websocket между агентом и службами Azure AD для повышения устойчивости к подключению 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Состояние выпуска 
11/25/2017: Выпущен для скачивания  
### <a name="fixed-issues"></a>Исправленные проблемы 
- Исправлены ошибки, связанные с кэшем DNS для сценариев прокси по умолчанию 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Состояние выпуска 
10/17/2017: Выпущен для скачивания  
### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения 
- Добавлена функция кэша DNS для исходящих соединений для добавления устойчивости от сбоев DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Состояние выпуска 
08/31/2017: Выпущен для скачивания  
### <a name="new-features-and-improvements"></a>Новые функции и внесенные улучшения 
- GA версия агента проверки подлинности Azure AD 

## <a name="next-steps"></a>Следующие шаги

- [Вход пользователей с помощью сквозной проверки подлинности Azure Active Directory](how-to-connect-pta.md)
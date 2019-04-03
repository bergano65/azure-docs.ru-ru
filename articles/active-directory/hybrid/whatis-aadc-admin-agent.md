---
title: Что такое агент Azure AD Connect администратор - Azure AD Connect | Документация Майкрософт
description: Сведения о средствах, используемых для синхронизации и мониторинга вашей локальной среды в Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49234472481e30cf74efa3e72ac0e4f31466fada
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884896"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Общие сведения об агенте администратора Azure AD Connect 
Агент Azure AD Connect администрирования является новым компонентом Azure Active Directory Connect, установленного на сервере Azure Active Directory Connect. Он позволяет собирать определенные данные из вашей среды Active Directory, которая помогает поддержки инженера, устранение неполадок при обратиться в службу поддержки корпорации Майкрософт.

При установке агента Azure AD Connect администрирования ожидает определенные запросы для данных из Azure Active Directory, получает запрошенные данные из среды синхронизации и отправляет его в Azure Active Directory, где они будут представлены в корпорацию Майкрософт поддерживают инженер.

Никоим образом не хранится сведения, которые агент Azure AD Connect администрирования извлекает из среды — он отображается только специалисту службы поддержки Майкрософт, чтобы помочь им исследование и устранение неполадок Azure Active Directory Connect связанные заявки, которое было открыто

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Способ установки агента Azure AD Connect администратора на сервере Azure AD Connect? 
После установки агента администратор, вы увидите следующие две новые программы в списке «Установка и удаление программ» панели управления сервера: 

![Агент-администратор](media/whatis-aadc-admin-agent/adminagent1.png)

Не удаляйте и не отменить установить эти программы, так как они являются важной частью установки Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Какие данные в службу синхронизации отображается инженеру службы Майкрософт?
При открытии обращение в службу поддержки, специалист службы поддержки Майкрософт можно видеть, для данного пользователя, релевантных данных в Active Directory, пространстве соединителя Active Directory на сервере Azure Active Directory Connect пространстве соединителя Azure Active Directory в Azure Сервер Active Directory Connect и метавселенной на сервере Azure Active Directory Connect.

Инженер службы поддержки Майкрософт не может изменить любые данные в системе и не отображаются все пароли.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Что делать, если я не хочу специалист службы поддержки Майкрософт для доступа к Мои данные? 
 
Если вы не хотите инженер поддержки Майкрософт для доступа к данным для позвонить в службу поддержки можно отключить это, изменив файл конфигурации службы, как описано ниже: 

  1.    Откройте **C:\Program Files\Microsoft Azure AD Connect администрирования Agent\AzureADConnectAdministrationAgentService.exe.config** в блокноте.
  2.    Отключить **UserDataEnabled** параметр, как показано ниже. Если **UserDataEnabled** параметр существует и имеет значение true, а затем присвойте ему значение false. Если параметр не существует, добавьте параметр, как показано ниже.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Сохраните файл конфигурации.
  4.    Перезапуск службы агента Azure AD Connect администрирования, как показано ниже

![Агент-администратор](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).
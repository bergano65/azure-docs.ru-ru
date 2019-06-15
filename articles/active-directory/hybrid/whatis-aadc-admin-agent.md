---
title: Что такое агент Azure AD Connect администратор - Azure AD Connect | Документация Майкрософт
description: Сведения о средствах, используемых для синхронизации и мониторинга вашей локальной среды в Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687223"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Общие сведения об агенте администратора Azure AD Connect 
Агент Azure AD Connect администрирования является новым компонентом Azure Active Directory Connect, можно установить на сервере Azure Active Directory Connect. Он позволяет собирать определенные данные из вашей среды Active Directory, которая помогает поддержки инженера, устранение неполадок при обратиться в службу поддержки корпорации Майкрософт. 

>[!NOTE]
>Агент-администратор не установлен и включен по умолчанию.  Чтобы собирать данные, чтобы помочь в службу поддержки в случаях, необходимо установить агент.

При установке агента Azure AD Connect администрирования ожидает определенные запросы для данных из Azure Active Directory, получает запрошенные данные из среды синхронизации и отправляет его в Azure Active Directory, где они будут представлены в корпорацию Майкрософт поддерживают инженер. 

Никоим образом не хранится сведения, которые агент Azure AD Connect администрирования извлекает из среды — он отображается только специалисту службы поддержки Майкрософт, чтобы помочь им исследование и устранение неполадок Azure Active Directory Connect связанные заявки, что вы открыли агента Azure AD Connect администрирования не устанавливается на сервер Azure AD Connect по умолчанию. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Установка агента Azure AD Connect администрирования на сервере Azure AD Connect 
Агент Azure AD Connect администрирования двоичные файлы помещаются в сервер AAD Connect. Чтобы установить агент, сделайте следующее: 



1. Откройте powershell в режиме администратора 
2. Перейдите в каталог, где оно будет расположен cd «C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles» 
3. Запустите приложение AADConnectAdminAgentSetup.exe 
 
Когда появится запрос, введите свои учетные данные глобального администратора Azure AD. 

>[!NOTE]
>Имеется известная проблема, где вам предложат ввести учетные данные несколько раз. Эта проблема будет устранена в следующем выпуске.

После установки агента вы увидите следующие две новые программы в списке «Установка и удаление программ» панели управления сервера: 

![Агент-администратор](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Какие данные в службу синхронизации отображается инженеру службы Майкрософт? 
При открытии обращение в службу поддержки Microsoft Support Engineer можно видеть, для данного пользователя, релевантных данных в Active Directory, пространстве соединителя Active Directory на сервере Azure Active Directory Connect пространстве соединителя Azure Active Directory в Azure Сервер Active Directory Connect и метавселенной на сервере Azure Active Directory Connect. 

Инженер службы поддержки Майкрософт не может изменить любые данные в системе и не отображаются все пароли. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Что делать, если я не хочу специалист службы поддержки Майкрософт для доступа к Мои данные? 
После установки агента, если не хотите, чтобы инженер поддержки Майкрософт для доступа к данным для позвонить в службу поддержки, функцию можно отключить, изменив файл конфигурации службы, как описано ниже: 

1.  Откройте **C:\Program Files\Microsoft Azure AD Connect администрирования Agent\AzureADConnectAdministrationAgentService.exe.config** в блокноте.
2.  Отключить **UserDataEnabled** параметр, как показано ниже. Если **UserDataEnabled** параметр существует и имеет значение true, а затем присвойте ему значение false. Если параметр не существует, добавьте параметр, как показано ниже.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Сохраните файл конфигурации.
4.  Перезапуск службы агента Azure AD Connect администрирования, как показано ниже

![Агент-администратор](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).

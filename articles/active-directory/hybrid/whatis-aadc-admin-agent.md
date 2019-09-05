---
title: Что такое Агент администрирования Azure AD Connect — Azure AD Connect | Документация Майкрософт
description: Сведения о средствах, используемых для синхронизации и мониторинга вашей локальной среды в Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305201"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Общие сведения об агенте администратора Azure AD Connect 
Агент администрирования Azure AD Connect — это новый компонент Azure Active Directory Connect, который можно установить на Azure Active Directory Connect сервере. Он используется для получения конкретных данных из среды Active Directory, которая помогает инженеру службы поддержки Майкрософт устранять неполадки при открытии обращения в службу поддержки. 

>[!NOTE]
>По умолчанию агент администратора не установлен и не включен.  Необходимо установить агент для сбора данных, чтобы помочь в обращении в службу поддержки.

При установке Агент администрирования Azure AD Connect ожидает определенные запросы данных от Azure Active Directory, получает запрошенные данные из среды синхронизации и отправляет их в Azure Active Directory, где они предоставляются службе поддержки Майкрософт. инструменталь. 

Информация, которую Агент администрирования Azure AD Connect извлекает из вашей среды, не хранится каким-либо образом. она отображается только специалисту службы поддержки Майкрософт, чтобы помочь им в исследовании и устранении неполадок Azure Active Directory Connect связанный вариант поддержки, который вы открыли Агент администрирования Azure AD Connect, по умолчанию не установлен на сервере Azure AD Connect. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Установка агента администрирования Azure AD Connect на сервере Azure AD Connect 

Предварительные требования:
1.  На сервере установлен Azure AD Connect
2.  На сервере установлен Azure AD Connect Health

![Агент администратора](media/whatis-aadc-admin-agent/adminagent0.png)

Двоичные файлы агента администрирования Azure AD Connect помещаются на сервер AAD Connect. Чтобы установить агент, выполните следующие действия.

1.  Открытие PowerShell в режиме администратора
2.  Перейдите в каталог, где приложение находится на компакт-диске "C:\Program Files\Microsoft Azure Active Directory Коннект\тулс".
3.  Запустите Конфигуреадминажент. ps1.

При появлении запроса введите учетные данные глобального администратора Azure AD. Это должны быть те же учетные данные, которые были указаны во время установки Azure AD Connect.

После установки агента вы увидите в списке "Установка и удаление программ" на панели управления сервера следующие две новые программы: 

![Агент администратора](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Какие данные в службе синхронизации отображаются для инженера службы Майкрософт? 
Когда вы открываете обращение в службу поддержки, специалист по служба поддержки Майкрософт может видеть, что для конкретного пользователя нужны данные в Active Directory, Active Directory пространство соединителя на Azure Active Directory Connect сервере, пространство соединителя Azure Active Directory в Azure. Active Directory подключить сервер и метавселенной на сервере Azure Active Directory Connect. 

Инженер служба поддержки Майкрософт не может изменить какие бы то ни было данные в системе и не увидит пароли. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Что делать, если я не хочу, чтобы специалист службы поддержки Майкрософт заработал с данными? 
Если после установки агента не требуется, чтобы инженер службы Майкрософт мог получить доступ к данным для обращения в службу поддержки, можно отключить эту функцию, изменив файл конфигурации службы, как описано ниже. 

1.  Откройте папку **C:\Program Files\Microsoft Azure AD Connect Administration ажент\азуреадконнектадминистратионажентсервице.ЕКСЕ.конфиг** в блокноте.
2.  Отключите параметр **усердатаенаблед** , как показано ниже. Если параметр **усердатаенаблед** существует и имеет значение true, задайте для него значение false. Если параметр не существует, добавьте параметр, как показано ниже.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Сохраните файл конфигурации.
4.  Перезапустите службу агента администрирования Azure AD Connect, как показано ниже.

![Агент администратора](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Следующие шаги
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).

---
title: Использование многофакторной аутентификации AAD
description: База данных Azure S'L и Azure Synapse поддерживают соединения от студии управления серверами S'L (SSMS) с использованием универсальной аутентификации Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255877"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Использование многофакторной аутентификации AAD с базой данных Azure S'L и аналитикой Azure Synapse (поддержка SSMS для МИД)
База данных Azure S'L и Azure Synapse поддерживают соединения от студии управления серверами S'L (SSMS) с использованием *универсальной аутентификации Active Directory.* В этой статье рассматриваются различия между различными вариантами аутентификации, а также ограничения, связанные с использованием универсальной аутентификации. 

**Скачивание последней версии SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Для всех функций, обсуждаемых в этой статье, используйте по крайней мере июля 2017, версия 17.2.  Самый последний диалоговый ящик соединения должен выглядеть так же, как и следующее изображение:
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Завершает поле имени пользователя.")  

## <a name="the-five-authentication-options"></a>Пять параметров аутентификации  

Active Directory Universal Authentication поддерживает два неинтерактивных метода аутентификации:
    - `Active Directory - Password`Проверки подлинности
    - `Active Directory - Integrated`Проверки подлинности

Существуют также две неинтерактивные модели аутентификации, которые могут быть использованы во многих различных приложениях (ADO.NET, JDCB, ODC и т.д.). Эти два метода никогда не приводят к всплывающие диалоговые коробки: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Интерактивный метод, который также поддерживает многофакторную аутентификацию Azure (MFA): 
- `Active Directory - Universal with MFA` 


Azure MFA помогает защитить доступ к данным и приложениям, а также удовлетворить потребность пользователей в простом процессе входа. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Интерактивная MFA с использованием Azure AD может привести к появлению всплывающего диалогового окна для реализации проверки.

Общие сведения о Многофакторной идентификации см. в [этой статье](../active-directory/authentication/multi-factor-authentication.md).
Инструкции по настройке см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Параметр доменного имени или идентификатора клиента Azure AD   

Начиная с [SSMS версии 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), пользователи, импортируемые в текущий каталог Active Directory из других каталогов Azure Active Directory в качестве гостевых пользователей, могут при подключении указывать доменное имя или идентификатор клиента Azure AD. К гостевым пользователям относятся пользователи, приглашенные из других каталогов Azure AD, учетные записи Майкрософт, такие как outlook.com, hotmail.com и live.com, или другие учетные записи, например, gmail.com. Эта информация позволяет службе **универсальной аутентификации Active Directory с MFA** определить правильный центр аутентификации. Этот параметр также является обязательным для поддержки учетных записей Майкрософт (MSA), таких как outlook.com, hotmail.com или live.com, и сторонних учетных записей. Все пользователи, которые хотят выполнить аутентификацию с помощью универсальной проверки подлинности, должны ввести свое доменное имя или идентификатор клиента Azure AD. Этот параметр представляет собой текущее значение доменного имени или идентификатора клиента Azure AD, с которым связан сервер Azure. Например, если сервер Azure связан с доменом Azure AD `contosotest.onmicrosoft.com`, где пользователь `joe@contosodev.onmicrosoft.com` размещен в качестве импортированного пользователя из домена Azure AD `contosodev.onmicrosoft.com`, то доменное имя, необходимое для аутентификации этого пользователя, — `contosotest.onmicrosoft.com`. Если пользователь является собственным пользователем Azure AD, связанным с сервером Azure, а не пользователем с учетной записью MSA, то доменное имя или идентификатор клиента не требуются. Чтобы ввести параметр (начиная с SSMS-версии 17.2), в диалоговом окне **Connect to Database,** заполните диалоговое поле, выбрав Active Directory - Universal с проверкой подлинности **MFA,** нажмите **Options,** заполните поле **имени пользователя,** а затем нажмите на вкладку **Connection Properties.** Проверьте **доменное имя AD или id-поле клиента,** а также предоставьте полномочия по аутентификации, такие как доменное имя **(contosotest.onmicrosoft.com)** или GUID.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Если вы работаете SSMS 18.x или позже, то доменное имя AD или идентификатор клиента больше не нужны гостем, потому что 18.x или позже автоматически распознает его.

   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Поддержка Azure AD B2B   
Пользователи Azure AD, поддерживаемые для сценариев Azure AD B2B, в качестве гостевых пользователей (см. Что [такое совместное использование Azure B2B)](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)могут подключаться к базе данных S'L и Azure Synapse только как члены группы, созданной в текущей Azure AD и отображенной вручную с помощью оператора Transact-S'L `CREATE USER` в данной базе данных. Например, если пользователь `steve@gmail.com` приглашен в Azure AD `contosotest` (с доменом Azure AD `contosotest.onmicrosoft.com`), то в каталоге Azure AD, который содержит участника `steve@gmail.com`, нужно создать группу Azure AD, например `usergroup`. Затем эта группа должна быть создана для конкретной базы данных (т.е. MyDatabase) по аминину Azure AD S'L или Azure AD DBO, выполнив `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` заявление ОТранс-СЗЛ. После создания пользователя базы данных пользователь `steve@gmail.com` сможет войти в `MyDatabase` с помощью параметра аутентификации SSMS `Active Directory – Universal with MFA support`. Группа пользователей по умолчанию имеет только разрешение на подключение, и дополнительные возможности доступа к данным потребуется предоставить обычным способом. Обратите внимание на то, что пользователь `steve@gmail.com` как гость должен установить флажок и добавить доменное имя AD `contosotest.onmicrosoft.com` в диалоговом окне **Свойства соединения** SSMS. Параметр Доменное имя AD или идентификатор клиента поддерживается только для параметра Universal with MFA connection (Универсальная с подключением MFA), в противном случае он неактивен.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Универсальные ограничения аутентификации для базы данных S'L и Azure Synapse
- SSMS и SqlPackage.exe — единственные инструменты, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
- SSMS версии 17.2 поддерживает одновременный доступ нескольких пользователей с помощью универсальной аутентификации с MFA. В версиях 17.0 и 17.1 только одна учетная запись Azure Active Directory может войти в экземпляр SSMS с использованием универсальной аутентификации. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение относится только к универсальной аутентификации Active Directory. Вы можете войти на разные серверы, используя проверку пароля Active Directory, встроенную аутентификацию Active Directory или аутентификацию SQL Server).
- SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
- SSMS версии 17.2 поддерживает мастер DacFx для экспорта, извлечения и развертывания данных базы данных. После того, как пользователь пройдет универсальную аутентификацию в диалоговом окне начальной аутентификации, мастер DacFx функционирует так же, как и для всех других методов аутентификации.
- Конструктор таблиц SSMS не поддерживает универсальную аутентификацию.
- Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.  
- Версия библиотеки аутентификации Active Directory (ADAL) для универсальной аутентификации была обновлена до последней выпущенной версии ADAL.dll — 3.13.9. Ознакомьтесь с [библиотекой аутентификации Active Directory версии 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Дальнейшие действия

- Инструкции по настройке см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
- Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера Базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).  
- [Настройка и управление аутентификацией активных каталогов Azure с помощью базы данных S'L или Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Платформа приложения уровня данных Microsoft® SQL Server® (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [СЗЛPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Импорт BACPAC-файла в новую базу данных SQL Azure](../sql-database/sql-database-import.md)  
- [Экспорт базы данных SQL Azure в BACPAC-файл](../sql-database/sql-database-export.md)  
- Интерфейс C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.  

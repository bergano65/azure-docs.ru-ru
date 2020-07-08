---
title: Использование многофакторной проверки подлинности Azure Active Directory
description: База данных SQL Azure, Azure SQL Управляемый экземпляр и Azure синапсе Analytics поддерживают подключения из SQL Server Management Studio (SSMS) с использованием Active Directory универсальной аутентификации.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 25d08e86fde47c24c134bc03b036c4f456314856
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983585"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Использование многофакторной проверки подлинности Azure Active Directory
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

База данных SQL Azure, Azure Управляемый экземпляр и Azure синапсе Analytics поддерживают подключения из SQL Server Management Studio (SSMS) с помощью *Azure Active Directory-Universal с* проверкой подлинности mfa. В этой статье обсуждаются отличия между различными вариантами проверки подлинности, а также ограничения, связанные с использованием универсальной проверки подлинности.

**Скачивание последней версии SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

Для всех функций, описанных в этой статье, используйте по крайней мере версию 17.2 за июль 2017 года. Диалоговое окно "Последнее подключение" должно выглядеть следующим образом:

  ![1mfa-universal-connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Заполнит поле имени пользователя.")  

## <a name="the-five-authentication-options"></a>Пять параметров аутентификации  

Универсальная проверка подлинности Active Directory поддерживает два неинтерактивных метода:
    - `Azure Active Directory - Password` аутентификация
    - `Azure Active Directory - Integrated` аутентификация

Существуют две неинтерактивные модели проверки подлинности, которые можно использовать во многообразных приложениях (ADO.NET, JDCB, ODC). При использовании этих двух методов никогда не отображаются всплывающие диалоговые окна:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Интерактивный метод, который также поддерживает многофакторную идентификацию Azure (MFA),:`Active Directory - Universal with MFA`

Azure MFA помогает защитить доступ к данным и приложениям, а также удовлетворить потребность пользователей в простом процессе входа. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Интерактивная MFA с использованием Azure AD может привести к появлению всплывающего диалогового окна для реализации проверки.

Описание многофакторной идентификации Azure [см. в](../../active-directory/authentication/multi-factor-authentication.md)этой статье.
Инструкции по настройке см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Параметр доменного имени или идентификатора клиента Azure AD

Начиная с [SSMS версии 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), пользователи, импортируемые в текущий каталог Active Directory из других каталогов Azure Active Directory в качестве гостевых пользователей, могут при подключении указывать доменное имя или идентификатор клиента Azure AD. К гостевым пользователям относятся пользователи, приглашенные из других каталогов Azure AD, учетные записи Майкрософт, такие как outlook.com, hotmail.com и live.com, или другие учетные записи, например, gmail.com. Эта информация позволяет службе **универсальной аутентификации Active Directory с MFA** определить правильный центр аутентификации. Этот параметр также является обязательным для поддержки учетных записей Майкрософт (MSA), таких как outlook.com, hotmail.com или live.com, и сторонних учетных записей. Все пользователи, которые хотят выполнить аутентификацию с помощью универсальной проверки подлинности, должны ввести свое доменное имя или идентификатор клиента Azure AD. Этот параметр представляет текущее доменное имя или идентификатор клиента Azure AD, с которым связан сервер Azure. Например, если сервер Azure связан с доменом Azure AD `contosotest.onmicrosoft.com`, где пользователь `joe@contosodev.onmicrosoft.com` размещен в качестве импортированного пользователя из домена Azure AD `contosodev.onmicrosoft.com`, то доменное имя, необходимое для аутентификации этого пользователя, — `contosotest.onmicrosoft.com`. Если пользователь является собственным пользователем Azure AD, связанным с сервером Azure, а не пользователем с учетной записью MSA, то доменное имя или идентификатор клиента не требуются. Чтобы ввести параметр (начиная с SSMS версии 17,2), в диалоговом окне **Подключение к базе данных** заполните диалоговое окно, выбрав **Active Directory-Universal с** проверкой подлинности MFA, щелкните **Параметры**, заполните поле **имя пользователя** и перейдите на вкладку **Свойства подключения** . Проверьте поле **доменное имя AD или идентификатор клиента** и укажите центр проверки подлинности, например доменное имя (**ContosoTest.onmicrosoft.com**) или идентификатор GUID идентификатора клиента.  
   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Если вы используете SSMS 18. x или более поздней версии, имя домена AD или идентификатор клиента больше не требуется для гостевых пользователей, поскольку 18. x или более поздней версии автоматически распознает его.

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Поддержка Azure AD B2B

Пользователи Azure AD, которые поддерживаются в сценариях Azure AD B2B в качестве гостевых пользователей (см. раздел [что такое служба совместной работы Azure B2B](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)), могут подключаться к базе данных SQL и Azure синапсе только как часть членов группы, созданной в текущем Azure AD, и сопоставляться вручную с помощью инструкции TRANSACT-SQL `CREATE USER` в данной базе данных. Например, если `steve@gmail.com` приглашен в Azure AD `contosotest` (с доменом Azure AD `contosotest.onmicrosoft.com` ), Группа Azure AD, например, `usergroup` должна быть создана в Azure AD, содержащей `steve@gmail.com` член. Затем эту группу необходимо создать для конкретной базы данных (то есть MyDatabase) с помощью администратора SQL Azure AD или Azure AD DBO, выполнив инструкцию Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . После создания пользователя базы данных пользователь `steve@gmail.com` сможет войти в `MyDatabase` с помощью параметра аутентификации SSMS `Active Directory – Universal with MFA support`. Группа пользователей по умолчанию имеет только разрешение на подключение, и дополнительные возможности доступа к данным потребуется предоставить обычным способом. Обратите внимание на то, что пользователь `steve@gmail.com` как гость должен установить флажок и добавить доменное имя AD `contosotest.onmicrosoft.com` в диалоговом окне **Свойства соединения** SSMS. Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра Universal with MFA connection (Универсальная с подключением MFA), в противном случае он неактивен.

## <a name="universal-authentication-limitations"></a>Ограничения универсальной проверки подлинности

- SSMS и SqlPackage.exe — единственные инструменты, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
- SSMS версии 17.2 поддерживает одновременный доступ нескольких пользователей с помощью универсальной аутентификации с MFA. Версии 17,0 и 17,1, ограничивают имя входа для экземпляра SSMS с использованием универсальной проверки подлинности для одной учетной записи Azure Active Directory. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение ограничено Active Directory универсальной проверки подлинности; вы можете войти на другой сервер, используя проверку подлинности Active Directory пароля, Active Directory встроенную проверку подлинности или SQL Server проверку подлинности).
- SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
- SSMS версии 17.2 поддерживает мастер DacFx для экспорта, извлечения и развертывания данных базы данных. После того, как пользователь пройдет универсальную аутентификацию в диалоговом окне начальной аутентификации, мастер DacFx функционирует так же, как и для всех других методов аутентификации.
- Конструктор таблиц SSMS не поддерживает универсальную аутентификацию.
- Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.  
- Версия библиотеки аутентификации Active Directory (ADAL) для универсальной аутентификации была обновлена до последней выпущенной версии ADAL.dll — 3.13.9. Ознакомьтесь с [библиотекой аутентификации Active Directory версии 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Дальнейшие действия

- Инструкции по настройке см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](logins-create-manage.md).  
- Убедитесь, что другие пользователи могут подключаться через брандмауэр: [Настройка правила брандмауэра на уровне сервера с помощью портал Azure](firewall-configure.md)  
- [Настройка проверки подлинности Azure Active Directory и управление ею с помощью базы данных SQL или Azure синапсе](authentication-aad-configure.md)  
- [Платформа приложения уровня данных Microsoft® SQL Server® (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Импорт BACPAC-файла в новую базу данных](database-import.md)  
- [Экспорт базы данных в BACPAC-файл](database-export.md)  
- Интерфейс C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.  

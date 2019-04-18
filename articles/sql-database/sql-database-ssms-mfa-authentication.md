---
title: С помощью AAD многофакторной проверки подлинности базы данных SQL Azure и хранилище данных SQL Azure | Документация Майкрософт
description: База данных SQL Azure и хранилище данных SQL Azure поддерживают подключения из SQL Server Management Studio (SSMS) с использованием универсальной проверки подлинности Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494907"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>С помощью AAD многофакторной проверки подлинности базы данных SQL Azure и хранилище данных SQL Azure (поддержка SSMS для MFA)
База данных SQL Azure и хранилище данных SQL Azure поддерживают подключения из SQL Server Management Studio (SSMS) с использованием *универсальной проверки подлинности Active Directory*. В этой статье рассматриваются различия между различные параметры проверки подлинности, а также ограничения, связанные с использованием универсальной проверки подлинности. 

**Скачивание последней версии SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Для всех функций, описанных в этой статье используйте по меньшей мере июля 2017 г., версии 17.2.  Последние диалогового окна соединения, должен выглядеть следующим образом:
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Заполнение поля \"Имя пользователя\"").  

## <a name="the-five-authentication-options"></a>Пять параметров аутентификации  

Универсальная аутентификация Active Directory поддерживает два метода с неинтерактивной проверкой подлинности:
    - `Active Directory - Password` Проверка подлинности
    - `Active Directory - Integrated` Проверка подлинности

Существуют две с неинтерактивной проверкой подлинности модели, которые могут использоваться во множестве различных приложений (ADO.NET, JDCB, ODC, и т.д.). Эти два метода в результате не всплывающие диалоговые окна: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Интерактивный метод, — которые также поддерживает Azure Multi-factor authentication (MFA) — 
- `Active Directory - Universal with MFA` 


Azure MFA помогает защитить доступ к данным и приложениям, при этом не усложняя процесс входа пользователей в систему. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Интерактивная MFA с использованием Azure AD может привести к появлению всплывающего диалогового окна для реализации проверки.

Общие сведения о Многофакторной идентификации см. в [этой статье](../active-directory/authentication/multi-factor-authentication.md).
Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Параметр доменного имени или идентификатора клиента Azure AD   

Начиная с [SSMS версии 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), пользователи, импортируемые в текущий каталог Active Directory из других каталогов Azure Active Directory в качестве гостевых пользователей, могут при подключении указывать доменное имя или идентификатор клиента Azure AD. К гостевым пользователям относятся пользователи, приглашенные из других каталогов Azure AD, учетные записи Майкрософт, такие как outlook.com, hotmail.com и live.com, или другие учетные записи, например, gmail.com. Эта информация позволяет службе **универсальной аутентификации Active Directory с MFA** определить правильный центр аутентификации. Этот параметр также является обязательным для поддержки учетных записей Майкрософт (MSA), таких как outlook.com, hotmail.com или live.com, и сторонних учетных записей. Все пользователи, которые хотят выполнить аутентификацию с помощью универсальной проверки подлинности, должны ввести свое доменное имя или идентификатор клиента Azure AD. Этот параметр представляет собой текущее значение доменного имени или идентификатора клиента Azure AD, с которым связан сервер Azure. Например, если сервер Azure связан с доменом Azure AD `contosotest.onmicrosoft.com`, где пользователь `joe@contosodev.onmicrosoft.com` размещен в качестве импортированного пользователя из домена Azure AD `contosodev.onmicrosoft.com`, то доменное имя, необходимое для аутентификации этого пользователя, — `contosotest.onmicrosoft.com`. Если пользователь является собственным пользователем Azure AD, связанным с сервером Azure, а не пользователем с учетной записью MSA, то доменное имя или идентификатор клиента не требуются. Чтобы ввести параметр (начиная с SSMS версии 17.2), заполните поля в диалоговом окне **Подключение к базе данных**, выбрав аутентификацию **Active Directory — универсальная с поддержкой MFA**, нажмите кнопку **Параметры**, заполните поле **Имя пользователя**, а затем щелкните вкладку **Свойства соединения**. Установите флажок **Доменное имя AD или идентификатор клиента** и укажите центр аутентификации, введя доменное имя (**contosotest.onmicrosoft.com**) или GUID идентификатора клиента.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Поддержка Azure AD B2B   
Пользователи Azure AD, поддерживаемые в качестве гостевых пользователей в сценариях Azure AD B2B (см. раздел [Что такое служба совместной работы Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)), могут подключиться к Базе данных SQL и хранилищу данных SQL только в составе группы, созданной в текущем каталоге Azure AD и вручную сопоставленной в заданной базе данных с помощью инструкции `CREATE USER` Transact-SQL. Например, если пользователь `steve@gmail.com` приглашен в Azure AD `contosotest` (с доменом Azure AD `contosotest.onmicrosoft.com`), то в каталоге Azure AD, который содержит участника `steve@gmail.com`, нужно создать группу Azure AD, например `usergroup`. Затем эту группу необходимо создать для конкретной базы данных (то есть MyDatabase), администратор Azure AD SQL или Azure AD DBO, выполнив Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` инструкции. После создания пользователя базы данных пользователь `steve@gmail.com` сможет войти в `MyDatabase` с помощью параметра аутентификации SSMS `Active Directory – Universal with MFA support`. Группа пользователей по умолчанию имеет только разрешение на подключение, и дополнительные возможности доступа к данным потребуется предоставить обычным способом. Обратите внимание на то, что пользователь `steve@gmail.com` как гость должен установить флажок и добавить доменное имя AD `contosotest.onmicrosoft.com` в диалоговом окне **Свойства соединения** SSMS. Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра Universal with MFA connection (Универсальная с подключением MFA), в противном случае он неактивен.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Ограничения универсальной аутентификации для базы данных SQL и хранилища данных SQL
- SSMS и SqlPackage.exe — единственные инструменты, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
- SSMS версии 17.2 поддерживает одновременный доступ нескольких пользователей с помощью универсальной аутентификации с MFA. В версиях 17.0 и 17.1 только одна учетная запись Azure Active Directory может войти в экземпляр SSMS с использованием универсальной аутентификации. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение относится только к универсальной аутентификации Active Directory. Вы можете войти на разные серверы, используя проверку пароля Active Directory, встроенную аутентификацию Active Directory или аутентификацию SQL Server).
- SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
- SSMS версии 17.2 поддерживает мастер DacFx для экспорта, извлечения и развертывания данных базы данных. После того, как пользователь пройдет универсальную аутентификацию в диалоговом окне начальной аутентификации, мастер DacFx функционирует так же, как и для всех других методов аутентификации.
- Конструктор таблиц SSMS не поддерживает универсальную аутентификацию.
- Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.  
- Версия библиотеки аутентификации Active Directory (ADAL) для универсальной аутентификации была обновлена до последней выпущенной версии ADAL.dll — 3.13.9. Ознакомьтесь с [библиотекой аутентификации Active Directory версии 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Дальнейшие действия

- Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Предоставьте другим пользователям доступ к базе данных: [Контроль и предоставление доступа к Базе данных SQL и Хранилищу данных SQL](sql-database-manage-logins.md)  
- Убедитесь, что другие пользователи могут подключаться через брандмауэр. [Создание и управление правилами брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md)  
- [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](sql-database-aad-authentication-configure.md)  
- [Платформа приложения уровня данных Microsoft® SQL Server® (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Импорт BACPAC-файла в новую базу данных SQL Azure](../sql-database/sql-database-import.md)  
- [Экспорт базы данных SQL Azure в BACPAC-файл](../sql-database/sql-database-export.md)  
- Интерфейс C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.  

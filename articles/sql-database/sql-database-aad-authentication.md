---
title: Azure Active Directory
description: Узнайте о том, как использовать Active Directory Azure для проверки подлинности с помощью базы данных S'L, управляемых экземпляров и аналитики Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421109"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Использование аутентификации Azure Active Directory для аутентификации с помощью SQL

Активная аутентификация Azure Active Directory — это механизм подключения к базе данных Azure [S'L,](sql-database-technical-overview.md) [управляемому экземпляру,](sql-database-managed-instance.md)и [Azure Synapse Analytics (бывший хранилище данных Azure S'L)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) с помощью идентификаторов в Active Directory Azure (Azure AD). 

> [!NOTE]
> Эта статья относится как к серверу Azure S'L, так и к базе данных S'L и Azure Synapse. Для простоты используется база данных S'L, когда речь идет как о базе данных S'L, так и о Azure Synapse.

С помощью проверки подлинности Azure AD можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление удостоверениями позволяет использовать единое расположение для управления пользователями и упрощает управление разрешениями. Это дает такие преимущества:

- наличие альтернативного варианта вместо аутентификации SQL Server;
- возможность остановить увеличение количества пользователей на серверах баз данных;
- изменение паролей в одном расположении;
- клиенты могут управлять разрешениями базы данных с помощью внешних групп (Azure AD);
- возможность исключить хранение паролей, используя встроенную аутентификацию Windows и другие виды аутентификации, поддерживаемые Azure Active Directory.
- В механизме аутентификации Azure AD для аутентификации удостоверений на уровне базы данных используются пользователи автономной базы данных.
- Для приложений, подключающихся к Базе данных SQL, Azure AD поддерживает аутентификацию на основе маркеров.
- Аутентификация Azure AD поддерживает:
  - Идентификаторы только для облака Azure
  - Гибридные идентификаторы Azure AD, поддерживающие:
    - Облачная аутентификация с двумя вариантами в сочетании с бесшовной одной регистрацией (SSO): **сквозная** аутентификация и проверка **хэша пароля**
    - Федеративная аутентификация
  - Для получения дополнительной информации о методах аутентификации Azure AD и о том, какой из них выбрать, смотрите следующую статью:
    - [Выбор правильного метода аутентификации для гибридного решения для идентификации Azure Active Directory](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD поддерживает подключения из SQL Server Management Studio, использующие универсальную проверку подлинности Active Directory, в том числе Многофакторную идентификацию (MFA).  MFA обеспечивает надежную аутентификацию с использованием ряда простых вариантов проверки посредством телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Для получения дополнительной информации см. [службу поддержки SSMS для Azure AD MFA с базой данных S'L и Azure Synapse](sql-database-ssms-mfa-authentication.md)
- Azure AD поддерживает аналогичные подключения из SQL Server Data Tools (SSDT), использующие интерактивную аутентификацию Active Directory. Для получения дополнительной информации посетите [службу поддержки Active Directory Azure в инструментах данных серверных серверов (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Подключение к SQL Server на виртуальной машине Azure с использованием учетной записи Azure Active Directory не поддерживается. Вместо этого используйте учетную запись домена Active Directory.  

Для настройки и использования проверки подлинности Azure Active Directory выполните следующие действия.

1. Создайте и заполните каталог Azure AD.
2. Свяжите или измените каталог Active Directory, который сейчас связан с вашей подпиской Azure (этот шаг можно пропустить).
3. Создайте администратор ампереip-направителя Azure Active Directory для сервера базы данных Azure S'L, управляемого экземпляра или [Azure Synapse.](https://azure.microsoft.com/services/sql-data-warehouse/)
4. Настройте клиентские компьютеры.
5. Создайте учетные записи пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD.
6. Подключитесь к базе данных с помощью удостоверений Azure AD.

> [!NOTE]
> Чтобы узнать, как создавать и заполнять Azure AD, а затем настроить Azure AD с помощью управляемой базы данных Azure S'L и Azure Synapse, [см.](sql-database-aad-authentication-configure.md)

## <a name="trust-architecture"></a>Архитектура доверия

- Для поддержки собственной проверки подлинности Azure AD с использованием пароля пользователя учитывается только облачная часть, база данных SQL Azure и Azure AD.
- Для поддержки учетных данных Windows с одним ибезоданным (или пользователем/паролем для учетных данных Windows) используйте учетные данные Azure Active Directory из федеративного или управляемого домена, настроенного для бесшовной единой регистрации для сквозной и проверки подлинности хэша паролей. Для получения дополнительной [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md)информации см.
- Для поддержки федеративной проверки подлинности (или имени пользователя и пароля в качестве учетных данных Windows) требуется взаимодействие с блоком ADFS.

Для получения дополнительной информации о гибридных идентификаторах Azure AD, настройке и синхронизации см.

- Проверка подлинности хэша паролей - [Реализация синхронизации хэш-паролем с синхронизацией Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Сквозная аутентификация - [Активная проверка подлинности Каталог Azure](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Федеративная аутентификация - [Развертывание служб активной федерации каталогов в Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) и [Azure AD Connect и федерации](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Для примера федеративной аутентификации с инфраструктурой ADFS (или пользователем/паролем для учетных данных Windows) см. Стрелки обозначают пути обмена данными.

![схема проверки подлинности aad][1]

На следующей схеме показаны федерация, отношения доверия и отношения размещения. Все эти компоненты позволяют клиенту подключиться к базе данных, отправив маркер. Маркер проходит аутентификацию в Azure AD и становится доверенным для базы данных. Клиент 1 может представлять каталог Azure AD с собственными пользователями или федеративными пользователями. Клиент 2 представляет возможное решение, включая импортированных пользователей. В этом примере пользователи импортированы из федеративной службы Azure Active Directory, при этом службы ADFS синхронизированы с Azure Active Directory. Важно понимать, что для доступа к базе данных с использованием проверки подлинности Azure AD необходимо связать подписку размещения с Azure AD. Та же подписка должна использоваться для создания сервера S'L, в мещавхую базу данных Azure S'L или Azure Synapse.

![отношение подписки][2]

## <a name="administrator-structure"></a>Структура администраторов

При использовании аутентификации Azure AD имеются две учетные записи администратора для сервера базы данных s'L и управляемого экземпляра; исходный администратор сервера S'L и администратор Azure AD. Те же понятия применимы и к Azure Synapse. Создать в пользовательской базе данных первого пользователя автономной базы данных Azure AD может только администратор с учетной записью Azure AD. Администратор Azure AD может использовать для входа имя пользователя Azure AD или имя группы Azure AD. Если учетная запись администратора является учетной записью группы, ее может использовать любой участник группы. По этой причине для одного экземпляра SQL Server может существовать несколько администраторов Azure AD. Использование учетной записи группы в качестве учетной записи администратора повышает управляемость. Это позволяет централизованно добавлять и удалять членов группы в Azure AD, не изменяя пользователей или разрешения в базе данных SQL. За один раз можно настроить только одного администратора Azure AD (пользователя или группу).

![структура администраторов][3]

## <a name="permissions"></a>Разрешения

Чтобы создавать новых пользователей, у вас должно быть разрешение `ALTER ANY USER` в базе данных. Разрешение `ALTER ANY USER` можно предоставить любому пользователю базы данных. Разрешение `ALTER ANY USER` также предоставляется учетным записям администратора сервера и пользователям базы данных с разрешениями `CONTROL ON DATABASE` или `ALTER ON DATABASE` для этой базы данных, а также участникам роли `db_owner` в базе данных.

Для создания содержащегося пользователя базы данных в базе данных Azure S'L, управляемой экземпляре или Azure Synapse необходимо подключиться к базе данных или экземпляру с помощью интактификации Azure AD. Чтобы создать первого пользователя автономной базы данных, необходимо подключиться к ней с использованием учетной записи администратора Azure AD (который является владельцем базы данных). Это показано в [настройке и управлении аутентификацией active Directory Azure с помощью базы данных S'L или Azure Synapse.](sql-database-aad-authentication-configure.md) Любая аутентификация Azure AD возможна только в том случае, если админ AD Azure был создан для базы данных Azure S'L или Azure Synapse. Если учетная запись администратора Azure Active Directory удалена с сервера, то существующие пользователи Azure Active Directory, учетные записи которых были созданы ранее на сервере SQL Server, больше не смогут подключаться к базе данных, используя свои текущие учетные данные Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Функции и ограничения Azure AD

- Следующие члены Azure AD могут быть подготовлены на сервере Azure S'L или Azure Synapse:

  - Собственные члены — члены, созданные в Azure AD в управляемом домене или в домене клиента. Дополнительные сведения см. в статье [Добавление имени личного домена в Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md).
  - Участники домена Active Directory, федеративные с помощью Azure Active Directory на управляемом домене, настроенном для бесшовного единого входного с помощью проверки подлинности хэша или паролей. Для получения дополнительной информации см. [Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) и [Azure Active Directory Seamless Single Sign-On.](../active-directory/hybrid/how-to-connect-sso.md)
  - Импортированные члены из других каталогов Azure AD, являющиеся собственными или федеративными членами домена.
  - Группы Active Directory, созданные как группы безопасности.

- Пользователи Azure AD, входят `db_owner` в группу, которая играет роль сервера, не могут использовать синтаксис **[CREATE DATABASESCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** против базы данных Azure S'L и Azure Synapse. Отобразится следующая ошибка:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Предоставляйте роль `db_owner` напрямую отдельным пользователям Azure AD во избежание проблем с синтаксисом **CREATE DATABASE SCOPED CREDENTIAL**.

- Эти системные функции возвращают значения NULL при выполнении с помощью субъектов Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Управляемые экземпляры

- Принципы сервера Azure AD (логины) и пользователи поддерживаются в качестве функции предварительного просмотра для [управляемых экземпляров.](sql-database-managed-instance.md)
- Настройка принципов сервера Azure AD (логинов), отображаемых в группе Azure AD, поскольку владелец базы данных не поддерживается в [управляемых экземплярах.](sql-database-managed-instance.md)
    - Расширение этого заключается в том, что при `dbcreator` добавлении группы как части роли сервера пользователи из этой группы могут подключиться к управляемому экземпляру и создавать новые базы данных, но не смогут получить доступ к базе данных. Это происходит, так как новый владелец базы данных является системным администратором, а не пользователем Azure AD. Эта проблема не проявляется при добавлении роли сервера `dbcreator` для отдельного пользователя.
- Управление и выполнение заданий sl-агентами поддерживаются для принципов серверов Azure AD (логинов).
- Операции резервного копирования и восстановления базы данных могут быть выполнены с помощью субъектов сервера (имен для входа) Azure AD.
- Поддерживается аудит всех инструкций, связанных с субъектами сервера (именами для входа) Azure AD и событиями проверки подлинности.
- Поддерживается выделенное административное соединение для субъектов сервера (имен для входа) Azure AD, которые являются участниками роли сервера системного администратора.
    - Поддерживается с помощью служебной программы SQLCMD и SQL Server Management Studio.
- Триггеры входа поддерживаются для событий входа, поступающих от субъектов сервера (имен для входа) Azure AD.
- С помощью субъектов сервера (имен для входа) Azure AD можно настроить почту Service Broker и базы данных.


## <a name="connecting-using-azure-ad-identities"></a>Подключение с использованием удостоверений Azure AD

Проверка подлинности Azure Active Directory поддерживает следующие способы подключения к базе данных с помощью удостоверений Azure AD:

- Пароль Azure Active Directory.
- Встроенная служба Azure Active Directory.
- Универсальная служба Azure Active Directory с поддержкой MFA.
- Использование маркера проверки подлинности приложения

Следующие методы аутентификации поддерживаются для принципов сервера Azure AD (логины):

- Пароль Azure Active Directory.
- Встроенная служба Azure Active Directory.
- Универсальная служба Azure Active Directory с поддержкой MFA.


### <a name="additional-considerations"></a>Дополнительные сведения

- Для повышения управляемости рекомендуем подготовить специальную группу Azure AD от имени администратора.   
- Только один администратор Azure AD (пользователь или группа) может быть настроен для сервера базы данных Azure S'L или Azure Synapse в любое время.
  - Добавление принципов сервера Azure AD (логинов) для управляемых экземпляров позволяет создать несколько принципов сервера Azure `sysadmin` AD (логины), которые могут быть добавлены в роль.
- Только администратор Azure AD для Сервера S'L может первоначально подключиться к серверу базы данных Azure S'L, управляемому экземпляру или Azure Synapse с помощью учетной записи Active Directory Azure. Затем администратор Active Directory может настроить других пользователей базы данных Azure AD.   
- Мы рекомендуем установить время ожидания подключения в 30 секунд.   
- Проверку подлинности Azure Active Directory поддерживают SQL Server 2016 Management Studio и SQL Server Data Tools для Visual Studio 2015 (версии 14.0.60311.1, выпущенной в апреле 2016 г., или более поздней). (Проверку подлинности Azure AD поддерживает **поставщик данных .NET Framework для SQL Server**, требуется версия .NET Framework не ниже 4.6.) Поэтому для последних версий этих инструментов и приложений уровня данных (DAC и BACPAC-файлов) можно применять аутентификацию Azure AD.   
- Начиная с версии 15.0.1, служебные программы[SQLCMD](/sql/tools/sqlcmd-utility) и [BCP](/sql/tools/bcp-utility) поддерживают интерактивную аутентификацию Active Directory с возможностью MFA.
- Для SQL Server Data Tools для Visual Studio 2015 требуется версия Data Tools, выпущенная в апреле 2016 г. (14.0.60311.1), или более поздняя. Сейчас пользователи Azure AD не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Драйвер Microsoft JDBC 6.0 для SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) поддерживает проверку подлинности Azure AD. Вы можете также ознакомиться с [настройкой свойств подключения](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase не поддерживает проверку подлинности Azure AD.   
- База данных SQL поддерживает проверку подлинности Azure AD на портале Azure. Для этого используются колонки **Импорт базы данных** и **Экспорт базы данных**. Импорт и экспорт с использованием проверки подлинности Azure AD также можно выполнить с помощью команды PowerShell.   
- Аутентификация Azure AD поддерживается для базы данных S'L, управляемой экземпляре, и Azure Synapse с использованием CLI. Для получения дополнительной информации [см. Налаживание и управление аутентификацией Active Directory Azure с помощью базы данных S'L или Azure Synapse](sql-database-aad-authentication-configure.md) и [сервера S'L - az sql.](https://docs.microsoft.com/cli/azure/sql/server)

## <a name="next-steps"></a>Следующие шаги

- Чтобы узнать, как создавать и заполнять Azure AD, а затем настроить Azure AD с базой данных Azure S'L или Azure Synapse, [см. Нанастройка и управление аутентификацией active Directory Azure с помощью управляемой базы данных S'L, управляемой экземпляре или Azure Synapse.](sql-database-aad-authentication-configure.md)
- Для руководства по использованию принципов сервера Azure AD (логинов) с управляемыми экземплярами [см.](sql-database-managed-instance-aad-security-tutorial.md)
- Для получения обзора логинов, пользователей, ролей баз данных и разрешений в базе данных S'L см. [Логины, пользователи, роли в базе данных и разрешения.](sql-database-manage-logins.md)
- Дополнительные сведения о субъектах базы данных см. в [этой статье](https://msdn.microsoft.com/library/ms181127.aspx).
- Дополнительные сведения о ролях баз данных см. в статье [Роли уровня базы данных](https://msdn.microsoft.com/library/ms189121.aspx).
- Для синтаксиса по созданию принципов серверов Azure AD (логинов) для управляемых экземпляров см. [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)
- Дополнительные сведения о правилах брандмауэра см. в статье [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

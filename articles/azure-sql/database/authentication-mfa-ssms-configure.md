---
title: Настройка Многофакторной идентификации
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Узнайте, как использовать многофакторную проверку подлинности с помощью SSMS для базы данных SQL Azure, Управляемый экземпляр Azure SQL и Azure синапсе Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 791402f5d9648c8d235f8853de1b6c41f8082e1b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018340"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Настройка Многофакторной идентификации для SQL Server Management Studio и Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

В этой статье показано, как использовать многофакторную идентификацию (MFA) Azure Active Directory (Azure AD) с SQL Server Management Studio (SSMS). Azure AD MFA можно использовать при подключении SSMS или SqlPackage.exe к [базе данных SQL Azure](sql-database-paas-overview.md), [управляемый экземпляр SQL Azure](../managed-instance/sql-managed-instance-paas-overview.md) и [Azure синапсе Analytics (ранее — хранилище данных SQL Azure)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Общие сведения о многофакторной проверке подлинности см. [в статье универсальная проверка подлинности с использованием базы данных SQL, sql управляемый экземпляр и Azure синапсе (поддержка SSMS для MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> В оставшейся части этой статьи в качестве баз данных в базе данных SQL Azure, Azure SQL Управляемый экземпляр и Azure синапсе (прежнее хранилище данных SQL Azure) упоминается этот сервер, который ссылается на [сервер](logical-servers.md) , на котором размещены базы данных SQL Azure и Azure синапсе.

## <a name="configuration-steps"></a>Этапы настройки

1. **Настройка Azure Active Directory.** Дополнительные сведения см. в статьях [Управление каталогом Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Интеграция локальных удостоверений с Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) и [AzureADHelp](https://msdn.microsoft.com/library/azure/jj151815.aspx) и записях блога [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Добавление собственного имени домена в Azure AD) и [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory).
2. **Настройка MFA.** Пошаговые инструкции см. в разделах [Что такое Многофакторная идентификация Azure?](../../active-directory/authentication/multi-factor-authentication.md) и [Условный доступ (MFA) и база данных SQL Azure и хранилище данных](conditional-access-configure.md). (Для полного условного доступа требуется Azure Active Directory Premium. Azure AD Standard обеспечивает ограниченные возможности MFA).
3. **Настройка аутентификация Azure AD** . пошаговые инструкции см. в статьях [Подключение к базе данных sql, SQL управляемый экземпляр или Azure синапсе с использованием проверки подлинности Azure Active Directory](authentication-aad-overview.md).
4. **Скачивание SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Подключение с помощью универсальной аутентификации и SSMS

Ниже описано, как подключиться с помощью последней версии SSMS.

1. Чтобы подключиться с помощью универсальной проверки подлинности, в диалоговом окне **Подключение к серверу** в SQL Server Management Studio (SSMS) выберите **Active Directory-Universal с поддержкой MFA**. (Если отображается параметр **Универсальная проверка подлинности Active Directory**, значит, у вас не самая последняя версия SSMS.)

   ![1mfa-universal-connect](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Заполните поле **Имя пользователя** учетными данными Azure Active Directory в формате `user_name@domain.com`.

   ![1mfa-universal-connect-user](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Если вы подключаетесь от имени гостевого пользователя, вам больше не нужно заполнять поле доменного имени AD или идентификатора клиента для гостевых пользователей, так как SSMS 18. x или более поздней версии автоматически распознает его. Дополнительные сведения см. в статьях [Универсальная проверка подлинности с базой данных SQL, sql управляемый экземпляр и Azure синапсе (поддержка SSMS для MFA)](../database/authentication-mfa-ssms-overview.md).

   ![MFA-No-клиент-SSMS](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Однако если вы подключаетесь от имени гостевого пользователя с помощью SSMS 17. x или более ранней версии, необходимо нажать кнопку **Параметры**и в диалоговом окне **Свойства соединения** и заполнить поле **доменное имя AD или идентификатор клиента** .

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Выберите **Параметры** и укажите базу данных в диалоговом окне **Параметры** . (Если подключенный пользователь является гостевым пользователем (т. е. joe@outlook.com ), необходимо установить флажок и добавить текущее доменное имя AD или идентификатор клиента как часть параметров. Дополнительные сведения см. в статье [Универсальная проверка подлинности для Базы данных SQL и Хранилища данных SQL (поддержка SSMS для MFA)](../database/authentication-mfa-ssms-overview.md). Щелкните **Подключить**.  
5. Когда откроется диалоговое окно **Вход в учетную запись** , укажите учетную запись и пароль своего удостоверения Azure Active Directory. Пароль не требуется, если пользователь является частью домена в федерации с Azure AD.

   ![2mfa-sign-in](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Если для этой учетной записи не требуется MFA, то на этом этапе универсальной аутентификации устанавливается подключение. Пользователям, которым требуется MFA, следует выполнить приведенные ниже действия.
   >  

6. Могут отобразиться два диалоговых окна настройки MFA. Эта одноразовая операция зависит от параметра администратора MFA, то есть может быть необязательной. Для домена с поддержкой MFA этот шаг иногда бывает предварительно определен (например, домен требует от пользователя использовать смарт-карту и ПИН-код).

   ![3mfa-setup](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. В диалоговом окне второй одноразовой операции можно выбрать параметры метода аутентификации. Возможные параметры настраиваются администратором.

   ![4mfa-verify-1](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory отправляет вам информациею о подтверждении. Получив код проверки, введите его в поле **Введите проверочный код** и нажмите кнопку **Вход**.

   ![5mfa-verify-2](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Обычно по завершении проверки выполняется подключение SSMS при условии, что указаны действительные учетные данные и в брандмауэре разрешен доступ.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о многофакторной проверке подлинности см. [в статье универсальная проверка подлинности с использованием базы данных SQL, sql управляемый экземпляр и Azure синапсе (поддержка SSMS для MFA)](../database/authentication-mfa-ssms-overview.md).  
- Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](logins-create-manage.md).  
- Убедитесь, что другие пользователи могут подключаться через брандмауэр: [Настройка правила брандмауэра на уровне сервера с помощью портал Azure](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.

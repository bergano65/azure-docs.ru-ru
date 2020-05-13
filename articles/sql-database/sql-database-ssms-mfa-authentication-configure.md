---
title: Настройка Многофакторной идентификации
description: Узнайте, как использовать многофакторную проверку подлинности с помощью SSMS для базы данных SQL и Azure синапсе Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: has-adal-ref
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: e551ac54414cb3114a7d0905b1b2e59b0d16580a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198546"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Настройка Многофакторной идентификации для SQL Server Management Studio и Azure AD

В этом разделе показано, как настроить Многофакторную идентификацию (MFA) Azure Active Directory для SQL Server Management Studio. Azure AD MFA можно использовать при подключении SSMS или SqlPackage. exe к [базе данных SQL](sql-database-technical-overview.md) Azure и [Azure синапсе Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Обзор многофакторной идентификации базы данных SQL Azure см. в статье [Универсальная проверка подлинности с помощью базы данных SQL и Azure синапсе (поддержка SSMS для MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Эта статья относится к Azure SQL Server и к базе данных SQL и базам данных Azure синапсе, созданным на сервере Azure SQL. Для простоты база данных SQL используется при обращении к базе данных SQL и Azure синапсе.

## <a name="configuration-steps"></a>Шаги настройки

1. **Настройка Azure Active Directory.** Дополнительные сведения см. в статьях [Управление каталогом Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) и [AzureADHelp](https://msdn.microsoft.com/library/azure/jj151815.aspx) и записях блога [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Добавление собственного имени домена в Azure AD) и [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory).
2. **Настройка MFA** . пошаговые инструкции см. в статье [что такое многофакторная идентификация Azure?](../active-directory/authentication/multi-factor-authentication.md), [Условный доступ (MFA) с базой данных SQL Azure и Azure синапсе](sql-database-conditional-access.md). (Для полного условного доступа требуется Azure Active Directory Premium (Azure AD). Azure AD Standard обеспечивает ограниченные возможности MFA).
3. **Настройка базы данных SQL или Azure синапсе для аутентификация Azure AD** . пошаговые инструкции см. в статье [Подключение к базе данных SQL или Azure синапсе с помощью проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).
4. **Скачивание SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Для всех функций в этом разделе используйте по крайней мере версию 17.2 за июль 2017 года.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Подключение с помощью универсальной аутентификации и SSMS

В следующих шагах показано, как подключиться к базе данных SQL или Сазуре синапсе с помощью последней версии SSMS.

1. Чтобы подключиться с использованием универсальной аутентификации, в диалоговом окне **Соединение с сервером** выберите **Active Directory — универсальная с поддержкой MFA**. (Если отображается параметр **Универсальная проверка подлинности Active Directory**, значит, у вас не самая последняя версия SSMS.)  
   ![1mfa-universal-connect][1]  
2. Заполните поле **Имя пользователя** учетными данными Azure Active Directory в формате `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Если вы подключаетесь от имени гостевого пользователя, вам больше не нужно заполнять поле доменного имени AD или идентификатора клиента для гостевых пользователей, так как SSMS 18. x или более поздней версии автоматически распознает его. Дополнительные сведения см. [в статье универсальная проверка подлинности с помощью базы данных SQL и Azure синапсе (поддержка SSMS для MFA)](sql-database-ssms-mfa-authentication.md).
   ![MFA-No-клиент-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Однако если вы подключаетесь от имени гостевого пользователя с помощью SSMS 17. x или более ранней версии, необходимо нажать кнопку **Параметры**и в диалоговом окне **Свойства соединения** и заполнить поле **доменное имя AD или идентификатор клиента** .
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Как обычно для базы данных SQL и Azure синапсе, необходимо нажать кнопку **Параметры** и указать базу данных в диалоговом окне **Параметры** . (Если подключенный пользователь является гостевым (т. е. joe@outlook.com), нужно установить флажок и добавить текущее доменное имя AD или идентификатор клиента как часть параметров.) См. статью [Универсальная проверка подлинности с помощью базы данных SQL и Azure синапсе (поддержка SSMS для MFA)](sql-database-ssms-mfa-authentication.md). Щелкните **Подключить**.  
5. Когда откроется диалоговое окно **Вход в учетную запись** , укажите учетную запись и пароль своего удостоверения Azure Active Directory. Пароль не требуется, если пользователь является частью домена в федерации с Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Если для этой учетной записи не требуется MFA, то на этом этапе универсальной аутентификации устанавливается подключение. Пользователям, которым требуется MFA, следует выполнить приведенные ниже действия.
   >  
   
6. Могут отобразиться два диалоговых окна настройки MFA. Эта одноразовая операция зависит от параметра администратора MFA, то есть может быть необязательной. Для домена с поддержкой MFA этот шаг иногда бывает предварительно определен (например, домен требует от пользователя использовать смарт-карту и ПИН-код).  
   ![3mfa-setup][3]  
7. В диалоговом окне второй одноразовой операции можно выбрать параметры метода аутентификации. Возможные параметры настраиваются администратором.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory отправляет вам информациею о подтверждении. Получив код проверки, введите его в поле **Введите проверочный код** и нажмите кнопку **Вход**.  
   ![5mfa-verify-2][5]  

Обычно по завершении проверки выполняется подключение SSMS при условии, что указаны действительные учетные данные и в брандмауэре разрешен доступ.

## <a name="next-steps"></a>Дальнейшие действия

- Обзор многофакторной идентификации базы данных SQL Azure см. в статье универсальная проверка подлинности с помощью [базы данных SQL и Azure синапсе (поддержка SSMS для MFA)](sql-database-ssms-mfa-authentication.md).  
- Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
- Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера Базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


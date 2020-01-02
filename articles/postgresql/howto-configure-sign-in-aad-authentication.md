---
title: Использование Azure Active Directory — база данных Azure для PostgreSQL — один сервер
description: Узнайте, как настроить Azure Active Directory (AAD) для проверки подлинности с помощью базы данных Azure для PostgreSQL — Single Server.
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c929ac1c171547a4ff485fc43f0f329440f9c3b5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763646"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Использование Azure Active Directory для проверки подлинности с помощью PostgreSQL

В этой статье описано, как настроить Azure Active Directory доступ к базе данных Azure для PostgreSQL, а также как подключиться с помощью маркера Azure AD.

> [!IMPORTANT]
> Аутентификация Azure AD для базы данных Azure для PostgreSQL в настоящее время доступна в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Настройка пользователя с правами администратора Azure AD

Только пользователь с правами администратора Azure AD может создавать и включать пользователей для проверки подлинности на основе Azure AD. Чтобы создать пользователя с правами администратора Azure AD, выполните следующие действия.

1. В портал Azure выберите экземпляр базы данных Azure для PostgreSQL, который вы хотите включить для Azure AD.
2. В разделе Параметры выберите Active Directory администратор:

![Настройка администратора Azure AD][2]

3. Выберите допустимого пользователя Azure AD в клиенте клиента, чтобы быть администратором Azure AD.

> [!IMPORTANT]
> При настройке администратора новый пользователь добавляется на сервер базы данных Azure для PostgreSQL с полными разрешениями администратора. Роль пользователя "Администратор Azure AD в базе данных Azure для PostgreSQL" будет `azure_ad_admin`.

Для каждого сервера PostgreSQL можно создать только одного администратора Azure AD и выбрать другой, перезапишу существующий администратор Azure AD, настроенный для этого сервера. Вы можете указать группу Azure AD, а не отдельного пользователя, чтобы иметь несколько администраторов. Обратите внимание, что после этого вы сможете войти с именем группы в целях администрирования.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Создание пользователей Azure AD в базе данных Azure для PostgreSQL

Чтобы добавить пользователя Azure AD в базу данных Azure для базы данных PostgreSQL, выполните следующие действия после подключения (см. следующий раздел о подключении).

1. Сначала убедитесь, что пользователь Azure AD `<user>@yourtenant.onmicrosoft.com` является допустимым пользователем в клиенте Azure AD.
2. Войдите в базу данных Azure для экземпляра PostgreSQL в качестве пользователя с правами администратора Azure AD.
3. Создание `<user>@yourtenant.onmicrosoft.com` ролей в базе данных Azure для PostgreSQL.
4. Сделайте `<user>@yourtenant.onmicrosoft.com` членом azure_ad_user ролей. Эта возможность должна быть предоставлена только пользователям Azure AD.

**Пример.**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Проверка подлинности пользователя в Azure AD не дает пользователю никаких разрешений на доступ к объектам в базе данных Azure для PostgreSQL. Необходимо предоставить пользователю необходимые разрешения вручную.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Создание групп Azure AD в базе данных Azure для PostgreSQL

Чтобы разрешить группе Azure AD доступ к базе данных, используйте тот же механизм, что и для пользователей, но вместо этого укажите имя группы:

**Пример.**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

При входе в систему члены группы будут использовать свои персональные маркеры доступа, но подписывать с именем группы, указанным в качестве имени пользователя.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Подключение к базе данных Azure для PostgreSQL с помощью Azure AD

На следующей высокоуровневой схеме показан рабочий процесс использования аутентификации Azure AD с базой данных Azure для PostgreSQL.

![Поток проверки подлинности][1]

Мы разработали интеграцию Azure AD для работы с общими средствами PostgreSQL, такими как PSQL, которые не поддерживаются в Azure AD и поддерживают указание имени пользователя и пароля при подключении к PostgreSQL. Мы передаем маркер Azure AD в качестве пароля, как показано на рисунке выше.

В настоящее время проверены следующие клиенты:

- psql commandline (используйте переменную ПГПАССВОРД для передачи маркера, см. ниже).
- Azure Data Studio (с использованием расширения PostgreSQL)
- Другие клиенты на основе либпк (например, общие платформы приложений и моделей ORM)

> [!NOTE]
> Учтите, что использование маркера Azure AD с pgAdmin в настоящее время не поддерживается, так как оно имеет жестко запрограммированное ограничение в 256 символов для паролей (которые превышают длину токена).

Ниже приведены действия, которые необходимо выполнить пользователю или приложению для проверки подлинности в Azure AD.

### <a name="step-1-authenticate-with-azure-ad"></a>Шаг 1. Аутентификация в Azure AD

Убедитесь, что [Azure CLI установлен](/cli/azure/install-azure-cli).

Вызов средства Azure CLI для проверки подлинности в Azure AD. Для этого необходимо предоставить идентификатор пользователя и пароль Azure AD.

```
az login
```

Эта команда запустит окно браузера на странице аутентификации Azure AD.

> [!NOTE]
> Для выполнения этих действий можно также использовать Azure Cloud Shell.
> Имейте в виду, что при извлечении маркера доступа Azure AD в Azure Cloud Shell потребуется явно вызвать `az login` и снова войти (в отдельном окне с кодом). После выполнения этого входа команда `get-access-token` будет работать должным образом.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Шаг 2. получение маркера доступа Azure AD

Вызовите средство Azure CLI, чтобы получить маркер доступа для пользователя, прошедшего проверку подлинности Azure AD, из шага 1, чтобы получить доступ к базе данных Azure для PostgreSQL.

Пример (для общедоступного облака):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Указанное выше значение ресурса должно быть указано в точности так, как показано. Для других облаков значение ресурса можно искать с помощью:

```shell
az cloud show
```

Для Azure CLI версии 2.0.71 и более поздней можно указать команду в следующей более удобной версии для всех облаков:

```shell
az account get-access-token --resource-type oss-rdbms
```

После успешной проверки подлинности Azure AD вернет маркер доступа:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Маркер — это базовая строка 64, которая кодирует все сведения о пользователе, прошедшем проверку подлинности, и предназначенное для службы "база данных Azure для PostgreSQL".

> [!NOTE]
> Срок действия маркера доступа составляет от 5 минут до 60 минут. Мы рекомендуем получить маркер доступа непосредственно перед запуском входа в базу данных Azure для PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Шаг 3. Использование токена в качестве пароля для входа с помощью PostgreSQL

При подключении необходимо использовать маркер доступа в качестве пароля пользователя PostgreSQL.

При использовании клиента командной строки `psql` маркер доступа должен передаваться через переменную среды `PGPASSWORD`, так как маркер доступа превышает длину пароля, которую `psql` может принимать напрямую:

Пример Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Пример для Linux и macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Теперь вы можете инициировать подключение к базе данных Azure для PostgreSQL, как обычно:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

Теперь вы прошли проверку подлинности на сервере PostgreSQL с помощью аутентификации Azure AD.

## <a name="token-validation"></a>Проверка токена

Проверка подлинности Azure AD в базе данных Azure для PostgreSQL гарантирует, что пользователь находится на сервере PostgreSQL и проверяет допустимость маркера, проверяя содержимое маркера. Выполняются следующие шаги проверки маркера:

-   Маркер подписан Azure AD и не был изменен
-   Маркер выдан Azure AD для клиента, связанного с сервером
-   Срок действия токена не истек
-   Токен предназначен для ресурса базы данных Azure для PostgreSQL (а не для другого ресурса Azure).

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Миграция существующих пользователей PostgreSQL в проверку подлинности на основе Azure AD

Вы можете включить проверку подлинности Azure AD для существующих пользователей. Следует учитывать два варианта:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Вариант 1. имя пользователя PostgreSQL соответствует имени субъекта-пользователя Azure AD.

В маловероятном случае, если существующие пользователи уже соответствуют именам пользователей Azure AD, вы можете предоставить им роль `azure_ad_user`, чтобы включить их для проверки подлинности Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Теперь они смогут выполнять вход с использованием учетных данных Azure AD вместо использования ранее настроенного пароля пользователя PostgreSQL.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Вариант 2. имя пользователя PostgreSQL отличается от имени субъекта-пользователя Azure AD.

Если пользователь PostgreSQL не существует в Azure AD или имеет другое имя пользователя, вы можете использовать группы Azure AD для проверки подлинности в качестве этого пользователя PostgreSQL. Вы можете перенести существующие пользователи базы данных Azure для PostgreSQL в Azure AD, создав группу Azure AD с именем, которое соответствует пользователю PostgreSQL, а затем предоставив роль azure_ad_user существующему пользователю PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Предполагается, что вы создали группу "Дбреадусер" в Azure AD. Пользователи, принадлежащие к этой группе, теперь смогут входить в базу данных от имени этого пользователя.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с общими концепциями [Azure Active Directory аутентификации с помощью базы данных Azure для PostgreSQL — Single Server](concepts-aad-authentication.md) .

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png

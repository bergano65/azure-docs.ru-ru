---
title: Используйте активный каталог Azure - База данных Azure для PostgreS-L - Единый сервер
description: Узнайте о том, как настроить активный каталог Azure (AAD) для проверки подлинности с помощью базы данных Azure для PostgreS-L - Единый сервер
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9f12849525daeea69ece6e81077446f062e8889
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384404"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Используйте активный каталог Azure для проверки подлинности с помощью PostgreS'L

В этой статье будут выработана работа по настройке доступа active Directory Azure с базой данных Azure для PostgreS'L и как подключиться с помощью маркера Azure AD.

> [!IMPORTANT]
> Проверка аутентификации Azure AD для базы данных Azure для PostgreS'L в настоящее время находится в открытом доступе.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Настройка пользователя AD Adin Azure

Только пользователь Azure Admin может создавать/включать пользователей для проверки подлинности на основе Azure AD. Для создания и пользователя Azure AdA Adin, пожалуйста, выполните следующие действия

1. На портале Azure выберите экземпляр базы данных Azure для PostgreS'L, который необходимо включить для Azure AD.
2. В настройках выберите Active Directory Admin:

![набор лазурного администратора рекламы][2]

3. Выберите действительного пользователя Azure AD в клиенте-клиенте администратором Azure AD.

> [!IMPORTANT]
> При настройке администратора новый пользователь добавляется в базу данных Azure для сервера PostgreS'L с полными разрешениями администратора. Роль будет играть пользователь Azure AD Admin в базе `azure_ad_admin`данных Azure для PostgreS'L.

Только один администратор Azure AD может быть создан на сервере PostgreS'L, а выбор другого будет перезаписывать существующий админ Azure Ad, настроенный на сервер. Можно указать группу Azure AD вместо отдельного пользователя, чтобы иметь несколько администраторов. Обратите внимание, что вы впишетесь с именем группы для целей администрирования.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Создание пользователей Azure AD в базе данных Azure для PostgreS'L

Чтобы добавить пользователя Azure AD в базу данных Azure для базы данных PostgreS'L, выполните следующие шаги после подключения (см. более поздний раздел о том, как подключиться):

1. Сначала убедитесь, что пользователь `<user>@yourtenant.onmicrosoft.com` Azure AD является действительным пользователем в нанимательazадных AD.
2. Подпишитесь на базу данных Azure для экземпляра PostgreS'L в качестве пользователя Azure AD Adin.
3. Создание `<user>@yourtenant.onmicrosoft.com` роли в базе данных Azure для PostgreS'L.
4. Сделать `<user>@yourtenant.onmicrosoft.com` членом роли azure_ad_user. Это должно быть предоставлено только пользователям Azure AD.

**Примере:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Проверка подлинности пользователя через Azure AD не дает пользователю никаких разрешений на доступ к объектам в базе данных Azure для базы данных PostgreS'L. Вы должны предоставить пользователю необходимые разрешения вручную.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Создание групп Azure AD в базе данных Azure для PostgreS'L

Чтобы включить группу Azure AD для доступа к базе данных, используйте тот же механизм, что и для пользователей, но вместо этого укажите имя группы:

**Примере:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

При входе в систему члены группы будут использовать свои токены личного доступа, но подписываются с именем группы, указанным как имя пользователя.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Подключение к базе данных Azure для PostgreS'L с помощью Azure AD

Следующая диаграмма высокого уровня обобщает рабочий процесс использования аутентификации Azure AD с базой данных Azure для PostgreS'L:

![поток аутентификации][1]

Мы разработали интеграцию Azure AD для работы с общими инструментами PostgreS'L, такими как psql, которые не являются осведомленными Azure AD и поддерживают только указание имени пользователя и пароля при подключении к PostgreS'L. Мы передаем токен Azure AD в качестве пароля, показанного на рисунке выше.

В настоящее время мы протестировали следующих клиентов:

- psql командная линия (использовать переменную PGPASSWORD, чтобы передать токен, см. ниже)
- Студия данных Azure (с использованием расширения PostgreS'L)
- Другие клиенты на основе libpq (например, общие платформы приложений и ORM)

> [!NOTE]
> Пожалуйста, имейте в виду, что использование маркера Azure AD с pgAdmin в настоящее время не поддерживается, так как он имеет жестко ещёе ограничение в 256 символов для паролей (которое превышает токен).

Вот те шаги, которые пользователю/приложению необходимо будет сделать для проверки подлинности с помощью Azure AD, описанного ниже:

### <a name="step-1-authenticate-with-azure-ad"></a>Шаг 1: Проверка подлинности с помощью Azure AD

Убедитесь, что у вас [установлен Azure CLI.](/cli/azure/install-azure-cli)

Вызвать инструмент Azure CLI для проверки подлинности с помощью Azure AD. Он требует, чтобы вы дали свой идентификатор пользователя Azure AD и пароль.

```azurecli-interactive
az login
```

Эта команда запустит окно браузера на страницу аутентификации Azure AD.

> [!NOTE]
> Для выполнения этих шагов можно также использовать Azure Cloud Shell.
> Пожалуйста, имейте в виду, что при получении токена доступа Azure AD в оболочке Azure Cloud Shell вам необходимо будет явно позвонить `az login` и войти снова (в отдельном окне с кодом). После этого знака `get-access-token` в команде будет работать, как ожидалось.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Шаг 2: Извлечения токен доступа Azure AD

Вызвать инструмент Azure CLI для получения токена доступа для аутентифицированного пользователя Azure AD со ступени 1 для доступа к базе данных Azure для PostgreS'L.

Пример (для общедоступного облака):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Вышеупомянутая стоимость ресурса должна быть указана точно так, как показано на рисунке. Для других облаков значение ресурса можно искать с помощью:

```azurecli-interactive
az cloud show
```

Для версии Azure CLI 2.0.71 и более поздней, команда может быть указана в следующей более удобной версии для всех облаков:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

После успешной проверки подлинности Azure AD возвращает токен доступа:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Токен — строка Base 64, которая кодирует всю информацию о подлинном пользователе и предназначена для базы данных Azure для службы PostgreS'L.

> [!NOTE]
> Срок действия маркера доступа составляет от 5 минут до 60 минут. Мы рекомендуем вам получить токен доступа непосредственно перед началом входа в базу данных Azure для PostgreS'L.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Шаг 3: Используйте токен в качестве пароля для входа в систему с PostgreS'L

При подключении необходимо использовать токен доступа в качестве пароля пользователя PostgreS'L.

При использовании `psql` клиента командной строки токен доступа `PGPASSWORD` должен быть пройден через переменную среды, так как токен доступа превышает длину пароля, который `psql` может приниматься непосредственно:

Пример Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Пример Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Теперь вы можете инициировать подключение к базе данных Azure для PostgreS'L, как обычно:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Теперь вы проходят аутентификацию на сервере PostgreS'L с помощью аутентификации Azure AD.

## <a name="token-validation"></a>Проверка токенов

Аутентификация Azure AD в базе данных Azure для PostgreS'L гарантирует наличие пользователя на сервере PostgreS'L и проверяет достоверность токена, проверяя содержимое токена. Выполняются следующие шаги проверки токенов:

- Токен подписан Azure AD и не был подделан
- Токен был выпущен Azure AD для клиента, связанного с сервером
- Срок действия токена не истек
- Токен предназначен для базы данных Azure для ресурса PostgreS'L (а не другого ресурса Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Переход существующих пользователей PostgreS'L к аутентификации на основе Azure AD

Можно включить аутентификацию Azure AD для существующих пользователей. Есть два случая, чтобы рассмотреть:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Дело 1: Имя пользователя PostgreS'L соответствует главному имени пользователя Azure AD

В маловероятном случае, когда существующие пользователи уже соответствуют именам пользователей Azure AD, можно предоставить `azure_ad_user` им эту роль, чтобы включить их в аутентификацию Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Теперь они смогут входить в систему с учетными данными Azure AD вместо того, чтобы использовать ранее настроенный пароль пользователя PostgreS'L.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Дело 2: Имя пользователя PostgreS'L отличается от главного имени пользователя Azure AD

Если пользователь PostgreS'L либо не существует в Azure AD, либо имеет другое имя пользователя, можно использовать группы Azure AD для проверки подлинности в качестве этого пользователя PostgreS'L. Можно перенести существующую базу данных Azure для пользователей PostgreS'L в Azure AD, создав группу Azure AD с именем, которое соответствует пользователю PostgreS'L, а затем предоставив роль azure_ad_user существующему пользователю PostgreS'L:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Это предполагает, что вы создали группу "DBReadUser" в Azure AD. Пользователи, принадлежащие к этой группе, теперь смогут войти в базу данных в качестве этого пользователя.

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите общие концепции [для проверки подлинности активного каталога Azure с помощью базы данных Azure для PostgreS-L - Единый сервер](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png

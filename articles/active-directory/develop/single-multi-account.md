---
title: Приложения для публичных клиентов с одним и несколькими учетными клиентами Azure
description: Обзор приложений для публичных клиентов с одним и несколькими учетными клиентами.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881356"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Приложения для публичных клиентов с одним и несколькими учетными клиентами

Эта статья поможет вам понять типы, используемые в приложениях для публичных клиентов с одним и несколькими учетными клиентами, с акцентом на приложениях для публичных клиентов с одним счетом. 

Библиотека подлинности активных каталогов Azure (ADAL) моделирует сервер.  Вместо этого Библиотека аутентификации Майкрософт (MSAL) моделирует клиентское приложение.  Большинство приложений для Android считаются публичными клиентами. Публичный клиент — это приложение, которое не может надежно хранить тайну.  

MSAL специализируется на API-поверхности `PublicClientApplication` для упрощения и уточнения опыта разработки приложений, которые позволяют использовать только одну учетную запись одновременно. `PublicClientApplication`подклассифицировано `MultipleAccountPublicClientApplication`и `SingleAccountPublicClientApplication` .  Следующая диаграмма показывает взаимосвязь между этими классами.

![SingleAccountPublicClientApplication UML Класс Диаграмма](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Заявка на единую учетную запись общедоступного клиента

Класс `SingleAccountPublicClientApplication` позволяет создавать приложение на основе MSAL, которое позволяет входить в систему только одной учетной записи одновременно. `SingleAccountPublicClientApplication` имеет следующие отличия от `PublicClientApplication` :

- MSAL отслеживает в настоящее время вданный счет.
  - Если ваше приложение использует брокера (по умолчанию во время регистрации портала Порталaz) и устанавливается на устройство, на котором присутствует брокер, MSAL удостоверится, что учетная запись по-прежнему доступна на устройстве.
- `signIn`позволяет подписаться в учетной записи явно и отдельно от запроса областей.
- `acquireTokenSilent`не требует параметра учетной записи.  Если вы предоставляете учетную запись, и учетная запись, которую вы `MsalClientException` предоставляете, не соответствует текущей учетной записи, отслеживаемым MSAL, он будет брошен.
- `acquireToken`не позволяет пользователю переключать учетные записи. Если пользователь пытается переключиться на другую учетную запись, выбрасывается исключение.
- `getCurrentAccount`возвращает объект результата, который предоставляет следующее:
  - Булеан, указывающий, изменился ли учетная запись. Например, учетная запись может быть изменена в результате удаления с устройства.
  - Предыдущий счет. Это полезно, если вам необходимо сделать какую-либо локальную очистку данных, когда учетная запись удалена с устройства или когда новая учетная запись вписана.
  - ТекущийСчет.
- `signOut`удаляет с устройства любые токены, связанные с клиентом.  

Когда на устройстве установлен брокер по аутентификации Android, такой как Microsoft Authenticator или `signOut` Intune Company Portal и ваше приложение настроено на использование брокера, не удаляет учетную запись с устройства.

## <a name="single-account-scenario"></a>Сценарий единого счета

Следующий псевдо-код `SingleAccountPublicClientApplication`иллюстрирует использование .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Несколько публичных приложений для клиентов по счетам

Класс `MultipleAccountPublicClientApplication` используется для создания приложений на основе MSAL, которые позволяют одновременно входить в систему нескольких учетных записей. Это позволяет получать, добавлять и удалять учетные записи следующим образом:

### <a name="add-an-account"></a>Добавить учетную запись

Используйте одну или несколько `acquireToken` учетных записей в приложении, позвонив один или несколько раз.  

### <a name="get-accounts"></a>Получить учетные записи

- Позвоните, `getAccount` чтобы получить конкретную учетную запись.
- Позвоните, `getAccounts`чтобы получить список учетных записей, известных в настоящее время в приложении.

Ваше приложение не сможет перечислить все учетные записи платформы Майкрософт на устройстве, известном приложению брокера. Он может перечислять только учетные записи, которые были использованы вашим приложением.  Учетные записи, удаленные с устройства, не будут возвращены этими функциями.

### <a name="remove-an-account"></a>Удаление учетной записи

Удалить учетную `removeAccount` запись, позвонив с идентификатором учетной записи.

Если ваше приложение настроено на использование брокера, и на устройстве установлен брокер, счет не `removeAccount`будет удален у брокера при звонке.  Удаляются только токены, связанные с вашим клиентом.

## <a name="multiple-account-scenario"></a>Сценарий нескольких учетных записей

Следующий псевдо-код показывает, как создать приложение с несколькими учетными записями, перечислить учетные записи на устройстве и приобрести токены.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```

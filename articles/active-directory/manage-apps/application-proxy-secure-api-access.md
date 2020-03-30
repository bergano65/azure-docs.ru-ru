---
title: Доступ к напредся на aIS с прокси-приложением Azure AD
description: Прокси-сервер приложения Azure Active Directory позволяет родным приложениям безопасно получать доступ к AA и бизнес-логике, которые размещается в помещениях или на облачных VMs.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166006"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Безопасный доступ к предприимчивым AБИ с помощью прокси-приложения Azure AD

Могут быть aIS бизнес-логики, работающие на месте, или размещенные на виртуальных машинах в облаке. Ваши родные приложения для Android, iOS, Mac или Windows должны взаимодействовать с конечными точками API для использования данных или взаимодействия с пользователем. Прокси-приложение Azure AD Proxy и [библиотеки аутентификации активных каталогов Azure (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) позволяют вашим родным приложениям безопасно получить доступ к вашим народным AI. Прокси-сервер Azure Active Directory Application Proxy — это более быстрое и безопасное решение, чем открытие портов брандмауэра и контроль аутентификации и авторизации на уровне приложения. 

В этой статье вы получите доступ к решению Azure AD Application Proxy для хостинга веб-службы API, к которой могут получить доступ нативные приложения. 

## <a name="overview"></a>Обзор

На следующей диаграмме показан традиционный способ публикации предварительных AI. Такой подход требует открытия входящих портов 80 и 443.

![Традиционный доступ к API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

На следующей диаграмме показано, как можно использовать прокси-приложение Azure AD для безопасной публикации AI без открытия входящих портов:

![Доступ к API-приложению API aD приложения Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Прокси-сервер Azure AD Application Формирует основу решения, работая в качестве общедоступной конечной точки для доступа к API и обеспечивая аутентификацию и авторизацию. Вы можете получить доступ к AA с широкого спектра платформ с помощью библиотек [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Поскольку аутентификация и авторизация Azure AD Application Proxy построены поверх Azure AD, можно использовать Условную Access Azure AD для обеспечения доступа к AA, опубликованным через Application Proxy. Используйте Azure AD Join или Azure AD Hybrid, объединенный для настольных компьютеров, и Intune Managed для устройств. Вы также можете воспользоваться функциями Azure Active Directory Premium, такими как Azure Multi-Factor Authentication, и безопасностью [системы защиты идентификации Azure,](/azure/active-directory/active-directory-identityprotection)поддерживаемой машинным обучением.

## <a name="prerequisites"></a>Предварительные требования

Чтобы следовать этому пошаговому шагу, вам нужно:

- Доступ к каталогу Azure с учетной записью, которая может создавать и регистрировать приложения
- Пример веб-API и приложений для нативных клиентов из[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Публикация API через прокси-сервер приложений

Чтобы опубликовать API за пределами интрасети через Application Proxy, вы следуете тому же шаблону, что и для публикации веб-приложений. Для получения дополнительной информации [см. Tutorial: Добавьте приложение для удаленного доступа через приложение Proxy в Active Directory Azure.](application-proxy-add-on-premises-application.md)

Для публикации веб-API SecretAPI через приложение Proxy:

1. Создайте и опубликуйте образец проекта SecretAPI в виде ASP.NET веб-приложения на локальном компьютере или интрасети. Убедитесь, что вы можете получить доступ к веб-приложению локально. 
   
1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory**. Затем выберите **приложения Enterprise.**
   
1. В верхней части **приложения Enterprise - Страница Всех приложений,** выберите новое **приложение.**
   
1. На странице **приложения выберите** **приложения On-premises.** **Отображается собственная** страница приложения.
   
1. Если у вас нет установлен апогея подключения прокси-сервера, вам будет предложено установить его. Выберите **Скачать приложение прокси-коннектор** для загрузки и установки разъема. 
   
1. После установки подключения прокси-сервера приложения на странице **приложения Добавить свою собственную** страницу приложения:
   
   1. Рядом с **именем,** введите *SecretAPI*.
      
   1. Рядом с **внутренним Url**введите URL, который используется для доступа к API внутри интрасети.
      
   1. Убедитесь, что **предварительная аутентификация** настроена на **активный каталог Azure.** 
      
   1. Выберите **Добавить** в верхней части страницы и ждать создания приложения.
   
   ![Добавление приложения API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. На **приложениях Enterprise - Все приложения** страницы, выберите приложение **SecretAPI.** 
   
1. На странице **SecretAPI - Обзор** выберите **Свойства** в левой навигации.
   
1. Вы не хотите, чтобы AIS были доступны конечным пользователям в панели **MyApps,** поэтому установите **видимые для пользователей** **нет** в нижней части **страницы Свойств,** а затем выберите **Сохранить.**
   
   ![Не видимый для пользователей](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Вы опубликовали свой веб-aPI через Прокси-приложение Azure AD. Теперь добавьте пользователей, которые могут получить доступ к приложению. 

1. На странице **SecretAPI - Обзор** выберите **пользователей и группы** в левой навигации.
   
1. На странице **пользователей и групп** выберите **Добавление пользователя.**  
   
1. На странице **назначения Добавить** выберите **пользователей и группы.** 
   
1. На странице **пользователей и группы** ищите и выберите пользователей, которые могут получить доступ к приложению, в ключая, по крайней мере, себя. После выбора всех пользователей **выберите**. 
   
   ![Выберите и назначить пользователя](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Вернуться на страницу **Добавления назначения,** выберите **Назначить**. 

> [!NOTE]
> AA, которые используют интегрированную аутентификацию Windows, могут потребовать [дополнительных шагов.](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Регистрация родного приложения и предоставление доступа к API

Родные приложения — это программы, разработанные для использования на определенной платформе или устройстве. Прежде чем ваше родное приложение сможет подключиться к API и получить доступ к нему, необходимо зарегистрировать его в Azure AD. Следующие шаги показывают, как зарегистрировать родное приложение и предоставить ему доступ к веб-API, который вы опубликовали через Application Proxy.

Чтобы зарегистрировать нативное приложение AppProxyNativeTheAppSample:

1. На **странице** Обзора active Directory Azure выберите **регистрацию приложений,** а в верхней части панели **регистраций приложений** выберите **новую регистрацию.**
   
1. На странице **регистрации приложения:**
   
   1. Под **именем,** введите *AppProxyNativeAppSample*. 
      
   1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт). 
      
   1. Под **перенаправление URL**, падение вниз и выбрать **общественный клиент (мобильный & рабочего стола),** а затем ввести *https:\//appproxynativeapp*. 
      
   1. Выберите **регистр**и подождите, пока приложение будет успешно зарегистрировано. 
      
      ![Регистрация нового приложения](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Теперь вы зарегистрировали приложение AppProxyNativeAppSample в Active Directory Azure. Чтобы дать вашему родному приложению доступ к веб-API SecretAPI:

1. На странице > **регистрации приложений** Active **Directory**Обзор Azure выберите приложение **AppProxyNativeAppSample.** 
   
1. На странице **AppProxyNativeAppSample** выберите разрешения API в левой **навигации.** 
   
1. На странице **разрешений API** выберите **добавление разрешения.**
   
1. На первой странице **разрешений API запроса** выберите **API, который использует вкладка,** а затем выберите **SecretAPI.** 
   
1. На следующей странице **разрешений API запроса** выберите флажок рядом с **user_impersonation,** а затем выберите **разрешения «Добавить».** 
   
    ![Выбор API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Вернувшись на страницу **разрешений API,** можно выбрать **согласие админа Grant для Contoso,** чтобы предотвратить индивидуальное согласие других пользователей на приложение. 

## <a name="configure-the-native-app-code"></a>Настройка исходного кода приложения

Последним шагом является настройка родного приложения. Следующий фрагмент из *файла Form1.cs* в примере приложения NativeClient заставляет библиотеку ADAL приобрести токен для запроса вызова API и прикрепить его в качестве носителя к заголовку приложения. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Чтобы настроить нативное приложение для подключения к Каталогу Активных Azure и вызова API App Proxy, обновите значения заполнителя в файле *App.config* образца приложения NativeClient со значениями из Azure AD: 

- Вставьте **идентификатор каталога (арендатора)** в `<add key="ida:Tenant" value="" />` поле. Вы можете найти и скопировать это значение (GUID) со страницы **Обзор** любого из ваших приложений. 
  
- Вставьте **идентификатор приложения AppProxyNativeAppSample (клиент)** в полевых условиях. `<add key="ida:ClientId" value="" />` Вы можете найти и скопировать это значение (GUID) со страницы **Обзор** AppProxyNativeAtAppSample.
  
- Вставьте AppProxyNativeAppSample **перенаправить** URI `<add key="ida:RedirectUri" value="" />` в поле. Вы можете найти и скопировать это значение (URI) со страницы **аутентификации** AppProxyNativeAppSample. 
  
- Вставьте SecretAPI **Приложение ID** `<add key="todo:TodoListResourceId" value="" />` URI в поле. Вы можете найти и скопировать это значение (URI) со страницы SecretAPI **Expose API.**
  
- Вставьте **URL-адрес Главной страницы** SecretAPI в `<add key="todo:TodoListBaseAddress" value="" />` поле. Вы можете найти и скопировать это значение (URL) со страницы **брендинга** SecretAPI.

После настройки параметров создайте и запустите родное приложение. При выборе кнопки **«Вход в** систему» приложение позволяет войти в систему, а затем отображает экран успеха, чтобы подтвердить, что оно успешно подключено к SecretAPI.

![Успешно](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник: Добавьте приложение для удаленного доступа через приложение Proxy в Active Directory Azure](application-proxy-add-on-premises-application.md)
- [Быстрый запуск: Настройка клиентского приложения для доступа к web-аПО](../develop/quickstart-configure-app-access-web-apis.md)
- [Как включить приложения для натиных клиентов для взаимодействия с прокси-приложениями](application-proxy-configure-native-client-application.md)

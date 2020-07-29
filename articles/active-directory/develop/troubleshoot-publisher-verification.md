---
title: Устранение неполадок при проверке издателя — платформа удостоверений Майкрософт | Azure
description: В этой статье описаны способы устранения неполадок проверки издателя (предварительная версия) для платформы удостоверений Майкрософт с помощью вызова API-интерфейсов Microsoft Graph.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 3069e3caf81d9bb2f809b21c88383c419e3b90b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282982"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Устранение неполадок с проверкой издателя (предварительная версия)
Если вам не удается завершить процесс или возникло непредвиденное поведение при [проверке подлинности издателя (предварительная версия)](publisher-verification-overview.md), следует начать со следующих действий при получении ошибок или обнаружении непредвиденного поведения: 

1. Проверьте [требования](publisher-verification-overview.md#requirements) и убедитесь, что они выполнены.

1. Просмотрите инструкции, чтобы [отметить приложение как проверенное издателем](mark-app-as-publisher-verified.md), и убедитесь, что все шаги были выполнены успешно.

1. Ознакомьтесь со списком [типичных проблем](#common-issues).

1. Воспроизведите запрос с помощью [песочницы Graph](#making-microsoft-graph-api-calls), чтобы собрать дополнительную информацию и исключить любые проблемы в пользовательском интерфейсе.

## <a name="common-issues"></a>Распространенные проблемы
Ниже приведены некоторые распространенные проблемы, которые могут возникнуть в процессе. 

- **Я не знаю свой идентификатор Microsoft Partner Network (ИД MPN) или не знаю, кто является основным контактным лицом для этой учетной записи** 
    1. Перейдите на страницу [регистрации MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new).
    1. Войдите с помощью учетной записи пользователя в основном клиенте Azure AD организации. 
    1. Если учетная запись MPN уже существует, она будет распознана и добавлена в учетную запись. 
    1. Перейдите на страницу [профиля партнера](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile), где будет указан идентификатор MPN и основное контактное лицо учетной записи.

- **Я не могу узнать, кто является глобальным администратором Azure AD (также известен как администратор компании или администратор клиента), как их найти? Как насчет администратора приложения или другой роли администратора?**
    1. Войдите на [портал Azure AD](https://aad.portal.azure.com) используя учетную запись пользователя в основном клиенте организации.
    1. Перейдите в раздел [Управление ролями](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
    1. Выберите "Глобальный администратор" или требуемую роль администратора.
    1. Появится список пользователей, которым назначена эта роль.

- **Я не знаю, кто является администратором моей учетной записи MPN**. Перейдите на страницу управления пользователями [MPN](https://partner.microsoft.com/pcv/users) и отфильтруйте список пользователей, чтобы увидеть, какие пользователи находятся в различных ролях администратора.

- **Я получаю сообщение об ошибке, что мой идентификатор MPN недействителен или у меня нет доступа к нему.**
    1. Перейдите к [профилю партнера](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) и убедитесь в том, что: 
        - Идентификатор MPN указан правильно. 
        - Ошибки или "ожидающие действия" не отображаются, а состояние проверки в разделе юридического бизнес-профиля и сведений о партнере имеют значения "авторизирован" или "успешно".
    1. Перейдите на страницу [управления клиентами MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) и убедитесь, что клиент, в котором зарегистрировано приложение и с помощью которого вы выполняете вход, используя учетную запись пользователя, находится в списке связанных клиентов.
    1. Перейдите на страницу [управления клиентами MPN](https://partner.microsoft.com/pcv/users) и подтвердите, что вы входите в систему как глобальный администратор, администратор MPN или администратор учетных записей.

- **При входе на портал Azure AD не отображаются зарегистрированные приложения. Почему?** 
    Регистрация приложений могла быть создана с использованием другой учетной записи пользователя или в другом клиенте. Убедитесь, что вы вошли с использованием правильной учетной записи в клиент, где были созданы регистрации приложений.

- **Как узнать, кто является владельцем регистрации приложения в Azure AD?** 
    Войдя в клиент, где зарегистрировано приложение, перейдите в колонку регистрация приложений, щелкните приложение, а затем выберите "Владельцы".

## <a name="making-microsoft-graph-api-calls"></a>Выполнение вызовов API Microsoft Graph 

Если вы столкнулись с проблемой, но не можете понять, что вы видите в пользовательском интерфейсе, может быть полезно выполнить устранение неполадок с помощью вызовов Microsoft Graph, чтобы выполнить те же операции, которые можно выполнить на портале регистрации приложений. На этапе предварительной версии эти API-интерфейсы будут доступны только в бета-версии конечной точки Microsoft Graph.  

Самый простой способ выполнить эти запросы — использовать [песочницу Graph](https://developer.microsoft.com/graph/graph-explorer). Вы также можете рассмотреть другие варианты, например использовать [POST](https://www.postman.com/) или [вызвать веб-запросы](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7) с помощью PowerShell.  

Вы можете использовать Microsoft Graph, чтобы установить и отменить проверенного издателя приложения и проверить результат после выполнения одной из этих операций. Результат можно увидеть в объекте [приложения](/graph/api/resources/application?view=graph-rest-beta), соответствующем регистрации приложения, а также в любых [субъектах-службах](/graph/api/resources/serviceprincipal?view=graph-rest-beta), которые были созданы из этого приложения. Дополнительные сведения о связи между этими объектами см. в следующих статьях: [Объекты приложения и субъекта-службы в Azure Active Directory](app-objects-and-service-principals.md).  

Ниже приведены примеры некоторых полезных запросов.  

### <a name="set-verified-publisher"></a>Указание проверенного издателя 

Запрос

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Ответ 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* — это идентификатор MPN. 

### <a name="unset-verified-publisher"></a>Удаление проверенного издателя 

Запрос:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Ответ 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Получение сведений о проверенном издателе из приложения 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Получение сведений о проверенном издателе из субъекта-службы 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Справочник по ошибкам 

Ниже приведен список возможных кодов ошибок, которые могут быть получены при устранении неполадок в Microsoft Graph или в процессе регистрации приложений на портале.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

Указанный идентификатор MPN (<MPNID>) не существует, или у вас нет доступа к нему. Укажите допустимый идентификатор MPN и повторите попытку. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

Указан недопустимый идентификатор MPN (<MPNID>). Укажите допустимый идентификатор MPN и повторите попытку. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

Указан недопустимый идентификатор MPN (<MPNID>). Укажите допустимый идентификатор MPN и повторите попытку. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

Указанный идентификатор MPN (<MPNID>) не завершил процесс проверки. Завершите этот процесс в Центре партнеров и повторите попытку. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

Указан недопустимый идентификатор MPN (<MPNID>). Укажите допустимый идентификатор MPN и повторите попытку. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

Указан недопустимый идентификатор MPN (<MPNID>). Укажите допустимый идентификатор MPN и повторите попытку. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

Не удается найти целевое приложение (<AppId>). Укажите допустимый идентификатор приложения и повторите попытку. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Эта функция не поддерживается в клиенте Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Эта функция не поддерживается в клиенте, проверенном по электронной почте. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Целевое приложение (<AppId>) должно иметь набор доменов издателя. Укажите домен издателя и повторите попытку. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

Домен издателя целевого приложения (<publisherDomain>) не является проверенным доменом в этом клиенте. Проверьте домен клиента с помощью проверки DNS и повторите попытку. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Домен издателя целевого приложения (<publisherDomain>) не соответствует домену, используемому для проверки электронной почты в Центре партнеров (<pcDomain>). Убедитесь, что эти домены совпадают, и повторите попытку. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Вы не имеете права устанавливать свойство проверенного издателя в приложении (<AppId>) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

Идентификатор MPN не указан в тексте запроса, или тип содержимого запроса не имел значения application/JSON. 

### <a name="msanotsupported"></a>MSANotSupported  

Эта функция не поддерживается для учетных записей потребителей Майкрософт. Поддерживаются только приложения, зарегистрированные в Azure AD пользователем Azure AD. 

## <a name="next-steps"></a>Дальнейшие действия

Если вы проверили все предыдущие сведения и по-прежнему получаете сообщение об ошибке от Microsoft Graph, соберите как можно больше указанных ниже сведений, связанных с неудачным запросом, и [обратитесь в службу поддержки Майкрософт](developer-support-help-options.md#open-a-support-request).

- Отметка времени 
- CorrelationId 
- ObjectID или UserPrincipalName пользователя, выполнившего вход; 
- ObjectId целевого приложения;
- AppId целевого приложения;
- TenantId, где зарегистрировано приложение;
- Идентификатор MPN
- выполненный запрос REST; 
- возвращаемый код ошибки и сообщение. 

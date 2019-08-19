---
title: Service Fabric Azure. использование управляемого удостоверения с приложениями Service Fabric | Документация Майкрософт
description: Использование управляемых удостоверений из кода приложения Service Fabric
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 528e1b0a353cdcd716f9bca63c423af7a6f12641
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958243"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Как использовать управляемое удостоверение приложения Service Fabric для доступа к службам Azure

Service Fabric приложения могут использовать управляемые удостоверения для доступа к другим ресурсам Azure, поддерживающим проверку подлинности на основе Azure Active Directory. Приложение может получить [маркер доступа](../active-directory/develop/developer-glossary.md#access-token) , представляющий удостоверение, которое может быть назначено системой или назначено пользователю, и использовать его в качестве токена носителя для проверки подлинности в другой службе, также называемой защищенным [сервером ресурсов](../active-directory/develop/developer-glossary.md#resource-server). Маркер представляет удостоверение, назначенное приложению Service Fabric, и будет выдаваться только ресурсам Azure (включая приложения SF), которые совместно используют это удостоверение. Подробное [описание управляемых удостоверений](../active-directory/managed-identities-azure-resources/overview.md), а также различие между назначенными системой и назначенными пользователем удостоверениями см. в документации по управляемым удостоверениям. В рамках этой статьи мы будем называть приложение Service Fabric с поддержкой управляемых удостоверений в качестве [клиентского приложения](../active-directory/develop/developer-glossary.md#client-application) .

> [!IMPORTANT]
> Управляемое удостоверение представляет связь между ресурсом Azure и субъектом-службой в соответствующем клиенте Azure AD, связанном с подпиской, содержащей ресурс. Таким образом, в контексте Service Fabric управляемые удостоверения поддерживаются только для приложений, развернутых в качестве ресурсов Azure. 

> [!IMPORTANT]
> Прежде чем использовать управляемое удостоверение приложения Service Fabric, клиентскому приложению должен быть предоставлен доступ к защищенному ресурсу. Ознакомьтесь со списком [служб Azure, поддерживающих проверку подлинности Azure AD](/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) , чтобы проверить поддержку, а затем в соответствующую документацию службы, чтобы предоставить удостоверению доступ к интересующим ресурсам. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Получение маркера доступа с помощью REST API
В кластерах, поддерживающих управляемое удостоверение, среда выполнения Service Fabric предоставляет конечную точку localhost, которую приложения могут использовать для получения маркеров доступа. Конечная точка доступна на каждом узле кластера и доступна для всех сущностей на этом узле. Полномочные вызывающие объекты могут получить маркеры доступа, вызвав эту конечную точку и выполнив код проверки подлинности. код создается средой выполнения Service Fabric для каждой отдельной активации пакета кода службы и привязана к времени существования процесса, в котором размещается пакет кода службы.

В частности, среда службы Service Fabric с поддержкой управляемых удостоверений будет заполнена следующими переменными:
- "MSI_ENDPOINT": конечная точка localhost, полная с путем, версия API и параметры, соответствующие управляемому удостоверению этой службы.
- "MSI_SECRET": код проверки подлинности, который является непрозрачной строкой и однозначно представляет службу на текущем узле

> [!NOTE]
> Имена "MSI_ENDPOINT" и "MSI_SECRET" ссылаются на предыдущее обозначение управляемых удостоверений ("Управляемое удостоверение службы"), которые теперь являются устаревшими. Имена также согласуются с эквивалентными именами переменных среды, используемыми другими службами Azure, которые поддерживают управляемые удостоверения.

> [!IMPORTANT]
> Код приложения должен рассматривать значение переменной среды "MSI_SECRET" как конфиденциальные данные. она не должна регистрироваться или распространяться иным образом. Код проверки подлинности не имеет значения, находящегося за пределами локального узла, или после завершения процесса, в котором размещена служба, но он представляет удостоверение службы Service Fabric, поэтому следует рассматриваться с теми же мерами предосторожности, что и у маркера доступа.

Чтобы получить маркер, клиент выполняет следующие действия:
- формирует универсальный код ресурса (URI) путем сцепления управляемой конечной точки удостоверения (значение MSI_ENDPOINT) с версией API и ресурсом (аудиторией), требуемым для токена.
- создает HTTP-запрос GET для указанного URI.
- добавляет код проверки подлинности (значение MSI_SECRET) в качестве заголовка в запрос
- отправляет запрос

Успешный ответ будет содержать полезные данные JSON, представляющие результирующий маркер доступа, а также метаданные, описывающие его. Неудачный ответ также будет включать объяснение сбоя. Дополнительные сведения об обработке ошибок см. ниже.

Маркеры доступа будут кэшироваться Service Fabric на различных уровнях (узел, кластер, служба поставщика ресурсов), поэтому успешный ответ не обязательно означает, что маркер был выдан непосредственно в ответ на запрос пользовательского приложения. Маркеры будут кэшироваться меньше времени существования, поэтому приложение гарантированно получит действительный токен. Рекомендуется, чтобы код приложения кэширует все маркеры доступа, которые он получает. ключ кэширования должен включать аудиторию (производную от). 


Пример запроса:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
где:

| Элемент | Описание |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Управляемая конечная точка удостоверения для приложений Service Fabric, предоставляемых через переменную среды MSI_ENDPOINT. |
| `api-version` | Параметр строки запроса, указывающий версию API службы токенов управляемого удостоверения; Сейчас единственным допустимым значением является `2019-07-01-preview`и может быть изменено. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Это будет отражено в `aud` утверждении (аудитории) выданного маркера. В этом примере запрашивается маркер для доступа к Azure Key Vault, чей URI идентификатора https://keyvault.azure.com/ приложения —. |
| `Secret` | Поле заголовка HTTP-запроса, необходимое для службы Service Fabric управляемого маркера идентификации для служб Service Fabric для проверки подлинности вызывающего объекта. Это значение предоставляется средой выполнения SF через переменную среды MSI_SECRET. |


Пример ответа:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
где:

| Элемент | Описание |
| ------- | ----------- |
| `token_type` | Тип токена; в данном случае это маркер доступа носителя, который означает, что субъект (Bearer) этого маркера является предполагаемой темой маркера. |
| `access_token` | Запрашиваемый маркер доступа. При вызове защищенного REST API маркер внедряется в поле `Authorization` заголовка запроса в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `expires_on` | Метка времени истечения срока действия маркера доступа; представляется в виде числа секунд с "1970-01-01T0:0: 0Z UTC" и соответствует `exp` утверждению маркера. В этом случае срок действия маркера истекает в 2019-08-08T06:10:11 + 00:00 (в RFC 3339).|
| `resource` | Ресурс, для которого был выдан маркер доступа, указанный с помощью `resource` параметра строки запроса запроса; соответствует утверждению "AUD" маркера. |


## <a name="acquiring-an-access-token-using-c"></a>Получение маркера доступа с помощьюC#
Приведенное выше значение превращается в C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Доступ к Key Vault из Service Fabric приложения с помощью управляемого удостоверения
Этот пример строится на примере выше, чтобы продемонстрировать доступ к секрету, хранящемуся в Key Vault с помощью управляемого удостоверения.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }
```

## <a name="error-handling"></a>Обработка ошибок
В поле "код состояния" заголовка HTTP-ответа указано состояние успешного выполнения запроса. состояние "200 ОК" указывает на успешное выполнение, и ответ будет включать маркер доступа, как описано выше. Ниже приведено краткое перечисление возможных ответов об ошибках.

| Код состояния | Причина ошибки | Способ устранения |
| ----------- | ------------ | ------------- |
| 404. Не найдено. | Неизвестный код проверки подлинности, или приложению не было назначено управляемое удостоверение. | Устраните настройку приложения или код получения маркера. |
| 429 — слишком много запросов |  Достигнут предел регулирования, накладываемый AAD или SF. | Повторите попытку с экспоненциальным увеличением задержки. См. инструкции ниже. |
| Ошибка 4xx в запросе. | Один или несколько параметров запроса неверные. | Не выполняйте повторную попытку.  Для получения дополнительной информации просмотрите сведения об ошибке.  Ошибки 4xx — это ошибки времени разработки.|
| Ошибка 5xx из службы. | Подсистема управляемого удостоверения или Azure Active Directory вернула временную ошибку. | Повторите попытку через некоторое время. При повторной попытке может попасть в условие регулирования (429).|

При возникновении ошибки соответствующий текст HTTP-ответа содержит объект JSON со сведениями об ошибке:

| Элемент | Описание |
| ------- | ----------- |
| code | Код ошибки. |
| correlationId | Идентификатор корреляции, который можно использовать для отладки. |
| message | Подробное описание ошибки. **Описания ошибки могут в любое время измениться. Не зависят от самого сообщения об ошибке.**|

Пример ошибки:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Ниже приведен список типичных ошибок Service Fabric, характерных для управляемых удостоверений.

| Код | Сообщение | Описание | 
| ----------- | ----- | ----------------- |
| секресеадернотфаунд | Секрет не найден в заголовках запроса. | Код проверки подлинности не был предоставлен с запросом. | 
| манажедидентитинотфаунд | Управляемое удостоверение не найдено для указанного узла приложения. | Приложение не имеет удостоверения, или код проверки подлинности неизвестен. |
| аргументнуллоремпти | Параметр "Resource" не должен иметь значение null или быть пустой строкой. | Ресурс (аудитория) не указан в запросе. |
| InvalidApiVersion | Версия API "" не поддерживается. Поддерживаемая версия — "2019-07-01-Preview". | В URI запроса указана отсутствующая или неподдерживаемая версия API. |
| InternalServerError | Произошла ошибка. | В подсистеме управляемого удостоверения обнаружена ошибка, возможно, за пределами стека Service Fabric. Наиболее вероятной причиной является неверное значение, заданное для ресурса (проверьте наличие замыкающего символа "/"?) | 

## <a name="retry-guidance"></a>Рекомендации по использованию механизма повторов 

Как правило, единственным повторяемым кодом ошибки является 429 (слишком много запросов); внутренние ошибки сервера/5xx коды ошибок могут быть повторяемыми, хотя причина может быть постоянной. 

Ограничения регулирования применяются к числу вызовов управляемой подсистемы удостоверений — в частности, к "вышестоящим" зависимостям (управляемой службе удостоверений Azure или службе токенов безопасности). Service Fabric кэширует маркеры на различных уровнях конвейера, но учитывая распределенную природу вовлеченных компонентов, вызывающий может столкнуться с непоследовательным реагированием на регулирование (т. е. регулируется на одном узле или экземпляре приложения, но не на другой узел при запросе маркера для того же удостоверения.) Если задано условие регулирования, последующие запросы из этого же приложения могут завершаться ошибкой с кодом состояния HTTP 429 (слишком много запросов) до тех пор, пока не будет сброшено условие.  

Не рекомендуется выполнять запросы, так как регулирование выполняется повторно с экспоненциальной задержкой, как показано ниже. 

| Индекс вызова | Действие при получении 429 | 
| --- | --- | 
| 1 | Подождите 1 секунду и повторите попытку |
| 2 | Подождите 2 секунды и повторите попытку |
| 3 | Подождите 4 секунды и повторите попытку |
| 4 | Подождите 8 секунд и повторите попытку |
| 4 | Подождите 8 секунд и повторите попытку |
| 5 | Подождите 16 секунд и повторите попытку |

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure
Список ресурсов, поддерживающих Azure AD, и соответствующие идентификаторы ресурсов см. в статье [службы Azure, поддерживающие аутентификацию Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) .

## <a name="next-steps"></a>Следующие шаги
* [Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Предоставление приложению Azure Service Fabric доступа к другим ресурсам Azure](./how-to-grant-access-other-resources.md)

## <a name="see-also"></a>См. также

* Проверка [поддержки управляемого удостоверения](./concepts-managed-identity.md) в Azure Service Fabric

* [Развертывание нового](./configure-new-azure-service-fabric-enable-managed-identity.md) Кластер Azure Service Fabric с поддержкой управляемых удостоверений 

* [Включение управляемого удостоверения](./configure-existing-cluster-enable-managed-identity-token-service.md) в существующем кластере Azure Service Fabric
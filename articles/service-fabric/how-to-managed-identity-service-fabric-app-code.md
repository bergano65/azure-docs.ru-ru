---
title: Используйте управляемый итог с помощью приложения
description: Как использовать управляемые идентификаторы в коде приложения Azure Service Fabric для доступа к службам Azure. Эта функция предоставляется в общедоступной предварительной версии.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 59680ec7911f55c3dc49d8834b410a039aa435dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610324"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Как использовать управляемое удостоверение приложения Service Fabric для доступа к службам Azure (предварительный просмотр)

Приложения Service Fabric могут использовать управляемые идентификаторы для доступа к другим ресурсам Azure, которые поддерживают аутентификацию на основе Azure Active Directory. Приложение может получить [токен доступа,](../active-directory/develop/developer-glossary.md#access-token) представляющий его личность, который может быть назначен системой или назначен пользователем, и использовать его в качестве маркера "носителя", чтобы проверить подлинность другой службы, также известной как [защищенный сервер ресурсов.](../active-directory/develop/developer-glossary.md#resource-server) Токен представляет идентификацию, назначенную приложению Service Fabric, и будет выдаваться только ресурсам Azure (включая приложения SF), которые разделяют эту идентификацию. Обратитесь к управляемой документации [обзора идентификации](../active-directory/managed-identities-azure-resources/overview.md) для подробного описания управляемых идентификационных данных, а также к различию между системными и назначенными пользователями идентификаторами. Мы будем ссылаться на приложение Service Fabric с поддержкой управляемой идентификации в качестве [клиентского приложения](../active-directory/develop/developer-glossary.md#client-application) на протяжении всей этой статьи.

> [!IMPORTANT]
> Управляемая идентификаторная система представляет связь между ресурсом Azure и принципом службы в соответствующем клиенте Azure AD, связанном с подпиской, содержащей ресурс. Таким образом, в контексте системы обслуживания управляемые идентификаторы поддерживаются только для приложений, развернутых в качестве ресурсов Azure. 

> [!IMPORTANT]
> Перед использованием управляемой идентификации приложения Service Fabric клиенту должен быть предоставлен доступ к защищенному ресурсу. Пожалуйста, обратитесь к списку [служб Azure, которые поддерживают аутентификацию Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) для проверки поддержки, а затем к документации соответствующей службы для конкретных шагов по предоставлению идентификационным ресурсам, представляющим интерес. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Приобретение токена доступа с помощью REST API
В кластерах, включенных для управляемых идел, время выполнения Service Fabric предоставляет конечную точку локального поставщика, которую приложения могут использовать для получения токенов доступа. Конечная точка доступна на каждом узлах кластера и доступна для всех сущностей этого узла. Авторизованные абоненты могут получить токены доступа, позвонив по этой конечной точке и представив код аутентификации; код генерируется временем выполнения Service Fabric для каждой активации определенного пакета услуг и привязан к сроку службы, хулящему этот пакет кода обслуживания.

В частности, среда службы service Fabric с управляемой идентичностью будет посеяна со следующими переменными:
- 'MSI_ENDPOINT': конечная точка локального хоста, в комплекте с траекторией, версией API и параметрами, соответствующими управляемой идентификации этой службы
- 'MSI_SECRET': код аутентификации, который является непрозрачной строкой и однозначно представляет службу на текущем узлах

> [!NOTE]
> Названия "MSI_ENDPOINT" и "MSI_SECRET" относятся к предыдущему обозначению управляемых идентификационных данных ("Управляется сервисная идентичность"), и которые в настоящее время унижаются. Имена также согласуются с эквивалентными переменными именами среды, используемыми другими службами Azure, которые поддерживают управляемые идентификаторы.

> [!IMPORTANT]
> В коде приложения следует рассматривать значение переменной среды "MSI_SECRET" как конфиденциальные данные - она не должна быть зарегистрирована или иным образом распространена. Код проверки подлинности не имеет значения за пределами локального узла или после завершения процесса хостинга службы, но он представляет личность службы Service Fabric, и поэтому следует относиться с теми же мерами предосторожности, что и сам токен доступа.

Чтобы получить токен, клиент выполняет следующие действия:
- формирует URI путем совместимость управляемой точки идентификации (MSI_ENDPOINT значение) с версией API и ресурсом (аудиторией), необходимым для маркера
- создает запрос GET http для указанного URI
- добавляет код проверки подлинности (MSI_SECRET значение) в качестве заголовка в запрос
- отправляет запрос

Успешный ответ будет содержать полезную нагрузку JSON, представляющую полученный токен доступа, а также метаданные, описывающие его. Неудачный ответ будет также включать объяснение сбоя. Ниже приведены дополнительные сведения об обработке ошибок.

Токены доступа будут кэшироваться Service Fabric на различных уровнях (узел, кластер, служба поставщика ресурсов), поэтому успешный ответ не обязательно означает, что токен был выпущен непосредственно в ответ на запрос приложения пользователя. Токены будут кэшироваться меньше срока их службы, и поэтому приложение гарантированно получит действительный токен. Рекомендуется, чтобы код приложения кэшировал любые приобретаемые им токены доступа; ключ кэширования должен включать (производ) аудиторию. 


Пример запроса:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
где:

| Элемент | Описание |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Управляемая конечная точка идентификации для приложений Service Fabric, предоставляемая через MSI_ENDPOINT переменной среды. |
| `api-version` | Параметр строки запроса, определяющий версию API службы управляемых токенов идентификации; в настоящее время `2019-07-01-preview`единственное принятое значение является и может быть изменено. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Это будет отражено `aud` как (аудитория) претензии выданного токена. Этот пример запрашивает маркер для доступа к Убежище ключа Azure,\/которое URI App ID — https: /keyvault.azure.com/. |
| `Secret` | Поле заголовка запроса HTTP, требуемое службой управляемых токенов токенов Service Fabric для службы обслуживания Fabric для проверки подлинности вызывающего абонента. Это значение обеспечивается временем выполнения SF через MSI_SECRET переменной среды. |


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
| `token_type` | Тип токена; в этом случае маркер доступа "Bearer", что означает, что докладчик ('носитель') этого маркера является предполагаемым предметом токена. |
| `access_token` | Запрашиваемый маркер доступа. При вызове защищенного REST API маркер внедряется в поле `Authorization` заголовка запроса в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `expires_on` | Отметка времени истечения срока действия токена доступа; представлено как количество секунд от "1970-01-01T0:0:0"UTC" и соответствует `exp` претензии токена. В этом случае срок действия токена истекает в 2019-08-08T06:10:11-00:00 (в RFC 3339)|
| `resource` | Ресурс, для которого был выдан токен `resource` доступа, указанный через параметр строки запроса запроса; соответствует претензии токена 'aud'. |


## <a name="acquiring-an-access-token-using-c"></a>Приобретение токена доступа с помощью C #
Вышеупомянутое становится, в C з:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

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

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Доступ к Key Vault из приложения Service Fabric с использованием управляемой identity
Этот пример основан на вышеизложенном, чтобы продемонстрировать доступ к секрету, хранящимся в Key Vault с использованием управляемой идентификации.

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

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
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

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Обработка ошибок
Поле "код статуса" заголовка ответа HTTP указывает состояние успеха запроса; статус '200 OK' указывает на успех, и ответ будет включать маркер доступа, как описано выше. Ниже приводится краткое перечисление возможных ответов на ошибку.

| Код состояния | Причина ошибки | Способ устранения |
| ----------- | ------------ | ------------- |
| 404. Не найдено. | Неизвестный код проверки подлинности, или приложению не была присвоена управляемая идентификация. | Исправите настройку приложения или код приобретения токенов. |
| 429 — слишком много запросов |  Ограничение дроссельной заслонки достигнуто, введенное AAD или SF. | Повторите попытку с экспоненциальным увеличением задержки. См. инструкции ниже. |
| Ошибка 4xx в запросе. | Один или несколько параметров запроса неверные. | Не выполняйте повторную попытку.  Для получения дополнительной информации просмотрите сведения об ошибке.  Ошибки 4xx — это ошибки времени разработки.|
| 5xx Ошибка от обслуживания. | Управляемая подсистема идентификации или Активный каталог Azure вернули переходную ошибку. | Это безопасно повторить через некоторое время. Вы можете нажать регулирование состояние (429) при повторной попытке.|

При возникновении ошибки соответствующий орган ответа HTTP содержит объект JSON с деталями ошибки:

| Элемент | Описание |
| ------- | ----------- |
| код | Код ошибки. |
| correlationId | Идентификатор корреляции, который может быть использован для отладки. |
| message | Подробное описание ошибки. **Описания ошибок могут меняться в любое время. Не зависейте от самого сообщения об ошибке.**|

Ошибка образца:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Ниже приведен список типичных ошибок Service Fabric, специфичным для управляемых идентификационных данных:

| Код | Сообщение | Описание | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Секрет не найден в заголовках запросов. | Код проверки подлинности не был предоставлен с запросом. | 
| ManagedIdentityNotFound | Управляемый итог не найден для указанного хоста приложения. | Приложение не имеет удостоверения личности, или код проверки подлинности неизвестен. |
| АргументНуллорОпт | Параметр "ресурс" не должен быть нулевым или пустой строкой. | Ресурс (аудитория) в запросе не был представлен. |
| InvalidApiVersion | Api-версия '' не поддерживается. Поддерживаемая версия '2019-07-01-предварительный просмотр'. | Отсутствует или не поддерживается версия API, указанная в запросе URI. |
| InternalServerError | Произошла ошибка. | Ошибка была обнаружена в подсистеме управляемых идентификационных данных, возможно, за пределами стека Service Fabric. Наиболее вероятной причиной является неправильное значение, указанное для ресурса (проверка на трейлинг '/'?) | 

## <a name="retry-guidance"></a>Рекомендации по использованию механизма повторов 

Обычно единственным retryable кодом ошибки является 429 (слишком много запросов); внутренние ошибки сервера/5xx коды ошибок могут быть повторенее, хотя причина может быть постоянной. 

Ограничения на просерение данных применяются к количеству вызовов, сделанных в подсистему управляемых идентификационных данных, в частности на зависимости «вверх по течению» (служба управляемой идентификации Azure или безопасная служба токенов). Токены кэша Service Fabric на различных уровнях конвейера, но с учетом распределенного характера участвующих компонентов, вызывающий абонент может испытывать несовместимые ответы на регулирование (т.е. задушить один узел/экземпляр приложения, но не на другом узеле, запрашивая токен для той же идентификации). При установлении состояния регулирования последующие запросы из того же приложения могут выполнить неудачу с кодом состояния HTTP 429 (Слишком много запросов) до тех пор, пока условие не будет очищено.  

Рекомендуется, чтобы запросы не удалось из-за регулирования повторно с экспоненциальным резервного копирования, а именно: 

| Индекс вызова | Действие по получению 429 | 
| --- | --- | 
| 1 | Подождите 1 секунду и повторную попытку |
| 2 | Подождите 2 секунды и повторная попытка |
| 3 | Подождите 4 секунды и повторная попытка |
| 4 | Подождите 8 секунд и повторная попытка |
| 4 | Подождите 8 секунд и повторная попытка |
| 5 | Подождите 16 секунд и повторная попытка |

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure
В ней можно ознакомиться [с службами Azure, поддерживающими проверку подлинности Azure AD,](../active-directory/managed-identities-azure-resources/services-support-msi.md) для списка ресурсов, поддерживающих Azure AD, и их идентификаций ресурсов.

## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание приложения Azure Service Fabric с системой, назначенной управляемой личностью](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Azure Service Fabric с помощью управляемого удостоверения пользователя](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Предоставить доступ к другим ресурсам Azure](./how-to-grant-access-other-resources.md)
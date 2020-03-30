---
title: Поставщик кэша вывода ASP.NET для кэша Azure для Redis
description: Узнайте, как кэшировать ASP.NET вывода страницы с помощью Кэша Azure для Redis. Поставщик кэша вывода Redis представляет собой механизм внепроцессного хранения для выходных данных кэширования.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530331"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Поставщик кэша вывода ASP.NET для кэша Azure для Redis

Поставщик кэша вывода Redis представляет собой механизм внепроцессного хранения для выходных данных кэширования. Эти данные предназначены специально для полных HTTP-ответов (кэширование вывода страниц). Поставщик подключается к новой точке расширения поставщика вывода кэша, которая появилась в ASP.NET 4.

Чтобы использовать поставщик кэша вывода Redis, сначала настройте кэш, а затем приложение ASP.NET, используя пакет NuGet поставщика кэша вывода Redis. Этот раздел содержит информацию о том, как настроить приложение, чтобы оно использовало поставщик кэша вывода Redis. Дополнительные сведения о создании и настройке экземпляра кэша Azure для Redis см. в разделе [Создание кэша](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Сохранение выходных данных страницы ASP.NET в кэше

Чтобы настроить клиентское приложение в Visual Studio, используя пакет NuGet состояний сеансов кэша Azure для Redis, в меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем **Консоль диспетчера пакетов**.

Выполните следующую команду в окне `Package Manager Console`:

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Пакет NuGet поставщика кэша вывода Redis имеет зависимость от пакета StackExchange.Redis.StrongName. Если в проекте отсутствует пакет StackExchange.Redis.StrongName, то он будет установлен. Дополнительные сведения о пакете NuGet RedisOutputCacheProvider см. на странице NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Помимо пакета со строгим именем StackExchange.Redis.StrongName существует также версия с нестрогим именем StackExchange.Redis. Если ваш проект использует несильную версию StackExchange.Redis, вы должны удалить его; в противном случае вы будете испытывать конфликты именования в вашем проекте. Дополнительные сведения об этих пакетах см. в разделе [Настройка клиентов кэша .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Пакет NuGet скачивает и добавляет требуемые ссылки на сборку и добавляет следующий раздел в файл web.config. Этот раздел содержит необходимые настройки для приложения ASP.NET, позволяющие использовать поставщик кэша вывода Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Задайте для атрибутов значения из колонки своего кэша на портале Microsoft Azure и настройте другие параметры по своему усмотрению. Инструкции по доступу к свойствам кэша см. в разделе [Настройка параметров кэша Azure для Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Атрибут | Тип | Значение по умолчанию | Описание |
| --------- | ---- | ------- | ----------- |
| *Узла* | строка | "местный хозяин" | IP-адрес сервера Redis или имя хоста |
| *Порт* | положительное целое число | 6379 (не SSL)<br/>6380 (SSL) | Порт сервера Redis |
| *Accesskey* | строка | "" | Redis пароль сервера при включении авторизации Redis. Значение является пустой строкой по умолчанию, что означает, что поставщик состояния сеанса не будет использовать пароль при подключении к серверу Redis. **Если ваш сервер Redis находится в общедоступной сети, такой как Azure Redis Cache, обязательно включите авторизацию Redis для повышения безопасности и предоставьте безопасный пароль.** |
| *Ssl* | Логическое | **false** | Подключение к серверу Redis через SSL. Это значение **является ложным** по умолчанию, поскольку Redis не поддерживает SSL из коробки. **Если вы используете Кэш Azure Redis, который поддерживает SSL из коробки, не забудьте установить это верно, чтобы улучшить безопасность.**<br/><br/>Для новых кэшей не SSL порт по умолчанию запрещен. Укажите **истинное** для этого параметра для использования порта SSL. Дополнительные сведения о том, как настроить использование порта без SSL, см. в статье [Настройка кэша](cache-configure.md#access-ports) в разделе [Порты доступа](cache-configure.md). |
| *База данныхIdNumber* | положительное целое число | 0 | *Этот атрибут может быть указан только через веб.-конфигурацию или AppSettings.*<br/><br/>Укажите, какую базу данных Redis использовать. |
| *соединениеTimeoutInMilliseconds* | положительное целое число | Предоставлено StackExchange.Redis | Используется для установки *ConnectTimeout* при создании StackExchange.Redis.ConnectionMultiplexer. |
| *операцияTimeoutInMilliseconds* | положительное целое число | Предоставлено StackExchange.Redis | Используется для установки *SyncTimeout* при создании StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (Valid StackExchange.Redis соединение строки) | строка | *n/a* | Либо ссылка параметра на AppSettings или web.config, либо действительная строка подключения StackExchange.Redis. Этот атрибут может предоставить значения для *хоста,* *порта,* *accessKey,* *ssl*и других атрибутов StackExchange.Redis. Для более подробного изучения *строки connectionString* [см.](#setting-connectionstring) [Attribute notes](#attribute-notes) |
| *настройкиClassName*<br/>*настройкиМетодНое* | строка<br/>строка | *n/a* | *Эти атрибуты могут быть указаны только через веб.-конфигурацию или AppSettings.*<br/><br/>Используйте эти атрибуты для обеспечения строки соединения. *настройкиClassName* должен быть собранием квалифицированных классовых имен, который содержит метод, указанный *настройкамиMethodName*.<br/><br/>Метод, указанный *настройкамиMethod,* должен быть общедоступным, статичным и недействительным (не принимать никаких параметров) с типом возврата **строки.** Этот метод возвращает фактическую строку соединения. |
| *журналыClassName*<br/>*лесозаготовкиМетодНамик* | строка<br/>строка | *n/a* | *Эти атрибуты могут быть указаны только через веб.-конфигурацию или AppSettings.*<br/><br/>Используйте эти атрибуты для отладки приложения, предоставляя журналы из кэша состояния сеанса/выхода вместе с журналами с StackExchange.Redis. *loggingClassName* должен быть собранием квалифицированных названий класса, который содержит метод, указанный *журналомMethodName.*<br/><br/>Метод, указанный *журналомMethodName,* должен быть общедоступным, статичным и недействительным (не принимать никаких параметров) с типом возврата **System.IO.TextWriter**. |
| *Applicationname* | строка | Имя модуля текущего процесса или "/" | *Только sessionStateProvider*<br/>*Этот атрибут может быть указан только через веб.-конфигурацию или AppSettings.*<br/><br/>Префикс имени приложения для использования в кэше Redis. Клиент может использовать один и тот же кэш Redis для различных целей. Чтобы гарантировать, что клавиши сеанса не сталкиваются, его можно прикрепить к названию приложения. |
| *throwOnError* | Логическое | Да | *Только sessionStateProvider*<br/>*Этот атрибут может быть указан только через веб.-конфигурацию или AppSettings.*<br/><br/>Следует ли выбросить исключение при возникновении ошибки.<br/><br/>Для получения дополнительной информации о *throwOnError*, [см. Заметки о *throwOnError* ](#notes-on-throwonerror) в разделе [Атрибут примечаний.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *повторная попыткаTimeoutInMilliseconds* | положительное целое число | 5000 | *Только sessionStateProvider*<br/>*Этот атрибут может быть указан только через веб.-конфигурацию или AppSettings.*<br/><br/>Как долго повторить попытку, когда операция не удается. Если это значение *меньше, чем операцияTimeoutInMilliseconds,* поставщик не будет повторно делать повторную попытку.<br/><br/>Для получения дополнительной информации о *повторной попыткеTimeoutInMilliseconds,* см. [Примечания на *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) в разделе [атрибутпримечаний.](#attribute-notes) |
| *redisSerializerType* | строка | *n/a* | Угоняет категорию категории типа сборки класса, реализуемого в Microsoft.Web.Redis. ISerializer и содержит пользовательскую логику для сериализации и десеризацию значений. Для получения дополнительной информации см. [ *redisSerializerType* ](#about-redisserializertype) [Attribute notes](#attribute-notes) |

## <a name="attribute-notes"></a>Примечания атрибута

### <a name="setting-connectionstring"></a>Настройка *подключенияСтонг*

Значение *connectionString* используется в качестве ключа для получения фактической строки соединения из AppSettings, если такая строка существует в AppSettings. Если не найдено внутри AppSettings, значение *connectionString* будет использоваться в качестве ключа для получения фактической строки соединения из раздела web.config **ConnectionString,** если этот раздел существует. Если строка подключения не существует в AppSettings или разделе web.config **ConnectionString,** буквальное значение *connectionString* будет использоваться в качестве строки соединения при создании StackExchange.Redis.ConnectionMultiplexer.

Следующие примеры иллюстрируют, как используется *connectionString.*

#### <a name="example-1"></a>Пример 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

В `web.config`, использовать выше ключа в качестве параметра значение вместо фактического значения.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Пример 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

В `web.config`, использовать выше ключа в качестве параметра значение вместо фактического значения.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Пример 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Заметки о *throwOnError*

В настоящее время, если ошибка возникает во время операции сеанса, поставщик состояния сеанса будет заверять. Это выключает приложение.

Это поведение было изменено таким образом, чтобы поддерживать ожидания существующих пользователей ASP.NET состояний сеанса, а также предоставляя возможность действовать на исключения, при желании. Поведение по умолчанию по-прежнему выбрасывает исключение при возникновении ошибки, в соответствии с другими поставщиками состояния ASP.NET сеанса; существующий код должен работать так же, как и раньше.

Если вы установите *throwOnError* на **ложные,** то вместо того, чтобы бросать исключение, когда происходит ошибка, он будет не в тишине. Чтобы узнать, была ли ошибка, и если да, то узнайте, что было исключением, проверьте статическое свойство *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Заметки о *повторной попыткеTimeoutInMilliseconds*

Это обеспечивает некоторую логику повтора, чтобы упростить случай, когда некоторые операции сеанса должны повторить попытку сбоя из-за таких вещей, как сетевой сбой, а также позволяет контролировать тайм-аут повторную попытку или полностью отказаться от повторной попытки.

Если вы установите *повторную попыткуTimeoutInMilliseconds* на число, например 2000, то, когда операция сеанса завершается неудачей, она будет повторно выполнять попытку в течение 2000 миллисекунд, прежде чем рассматривать ее как ошибку. Таким образом, чтобы провайдер состояния сеанса применил эту логику повтора, просто назначайте тайм-аут. Первая повторная попытка произойдет через 20 миллисекунд, что достаточно в большинстве случаев, когда происходит сбой в сети. После этого, он будет повторить каждую секунду, пока он время. Сразу после тайм-аута, он будет повторить еще раз, чтобы убедиться, что он не будет отрезать тайм-аут (не более) на одну секунду.

Если вы не считаете, что вам нужна повторная попытка (например, при запуске сервера Redis на той же машине, что и приложение), или если вы хотите обрабатывать логику повтора самостоятельно, установите *повторную попыткуTimeoutInMilliseconds* до 0.

### <a name="about-redisserializertype"></a>О *redisSerializerType*

По умолчанию сериализация для хранения значений на Redis выполняется в двоичном формате, предоставленном классом **BinaryFormatter.** Используйте *redisSerializerType,* чтобы указать имя категоричного типа сборки класса, который реализует **Microsoft.Web.Redis.ISerializer** и имеет пользовательскую логику для сериализации и десеризывания значений. Например, вот класс json serializer с использованием JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Предполагая, что этот класс определен в сборке с именем **MyCompanyDll,** вы можете установить параметр *redisSerializerType,* чтобы использовать его:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Директива кэша вывода

Добавьте директиву OutputCache для каждой страницы, для которой требуется кэшировать выходные данные.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

В предыдущем примере кэшированные данные страницы остаются в кэше в течение 60 секунд, а для каждой комбинации параметров кэшируется другая версия страницы. Для получения дополнительной информации о [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)директиве OutputCache см.

Когда эти действия будут выполнены, приложение будет соответствующим образом настроено и сможет использовать поставщик кэша вывода Redis.

## <a name="third-party-output-cache-providers"></a>Поставщики кэша вывода сторонних сторон

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Апач Воспламенение](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Дальнейшие действия

Посетите страницу [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) (Поставщик состояний сеансов ASP.NET для кэша Azure для Redis).

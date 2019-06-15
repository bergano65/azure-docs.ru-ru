---
title: Поставщик кэша вывода ASP.NET для кэша Azure для Redis
description: Информация о том, как выходные данные страницы ASP.NET кэшируются с помощью кэша Azure для Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943873"
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
>Помимо пакета со строгим именем StackExchange.Redis.StrongName существует также версия с нестрогим именем StackExchange.Redis. Если проект использует версию не нестрогим именем StackExchange.Redis, необходимо удалить. в противном случае могут возникнуть конфликты имен в проекте. Дополнительные сведения об этих пакетах см. в разделе [Настройка клиентов кэша .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

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

| Атрибут | type | значение по умолчанию | Описание |
| --------- | ---- | ------- | ----------- |
| *узел* | string | «localhost» | Redis server IP-адрес или имя |
| *port* | положительное целое число | 6379 (без SSL)<br/>6380 (SSL) | Порт сервера redis |
| *accessKey* | string | "" | Пароль сервера redis, когда включена авторизация по Redis. Значение является пустой строкой по умолчанию, это означает, что поставщик состояний сеансов не будет использовать любой пароль, при подключении к серверу Redis. **Если сервер Redis находится в общедоступной сети, например кэш Redis для Azure, не забудьте включить Redis авторизации для повышения безопасности и укажите надежный пароль.** |
| *SSL* | Логическое | **значение false** | Надо ли подключаться к серверу Redis через протокол SSL. Это значение равно **false** по умолчанию Redis поддерживает SSL без дополнительной настройки. **При использовании кэша Redis для Azure по умолчанию, который поддерживает SSL обязательно присвойте этому параметру значение true для повышения безопасности.**<br/><br/>Для новых кэшей не SSL порт по умолчанию запрещен. Укажите **true** для этот параметр, чтобы использовать порт SSL. Дополнительные сведения о том, как настроить использование порта без SSL, см. в статье [Настройка кэша](cache-configure.md#access-ports) в разделе [Порты доступа](cache-configure.md). |
| *databaseIdNumber* | положительное целое число | 0 | *Этот атрибут может быть указан только с помощью web.config или AppSettings.*<br/><br/>Укажите, какие базы данных Redis. |
| *connectionTimeoutInMilliseconds* | положительное целое число | Предоставляемые StackExchange.Redis | Используется для задания *ConnectTimeout* при создании StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | положительное целое число | Предоставляемые StackExchange.Redis | Используется для задания *SyncTimeout* при создании StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (строка подключения допустимым StackExchange.Redis) | string | *н/д* | Параметр ссылки на AppSettings или web.config, в противном случае допустимую строку подключения StackExchange.Redis. Этот атрибут можно указать значения для *узла*, *порт*, *accessKey*, *ssl*и другие атрибуты StackExchange.Redis. Для ближе взглянуть *connectionString*, см. в разделе [параметр connectionString](#setting-connectionstring) в [атрибут заметки](#attribute-notes) раздел. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *н/д* | *Эти атрибуты могут быть заданы только с помощью web.config или AppSettings.*<br/><br/>Эти атрибуты можно используйте для указания строки подключения. *settingsClassName* должно быть имя сборки имени класса, содержащего метод, определяемый *settingsMethodName*.<br/><br/>Методом, заданным свойством *settingsMethodName* должен быть общедоступный статический и void (не принимает никаких параметров), с типом возвращаемого значения **строка**. Этот метод возвращает строку подключения. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *н/д* | *Эти атрибуты могут быть заданы только с помощью web.config или AppSettings.*<br/><br/>Эти атрибуты можно используйте для отладки приложения, предоставляя журналы из кэша состояния сеанса и вывода, а также журналы от StackExchange.Redis. *loggingClassName* должно быть имя сборки имени класса, содержащего метод, определяемый *loggingMethodName*.<br/><br/>Методом, заданным свойством *loggingMethodName* должен быть общедоступный статический и void (не принимает никаких параметров), с типом возвращаемого значения **System.IO.TextWriter**. |
| *ApplicationName* | string | Имя модуля текущего процесса или «/» | *Только SessionStateProvider*<br/>*Этот атрибут может быть указан только с помощью web.config или AppSettings.*<br/><br/>Префикс имени приложения для использования в кэше Redis. Клиент может использовать один и тот же кэш Redis для разных целей. Убедиться в том, что ключи сеансов не перекрывались, он может иметь префикс с именем приложения. |
| *throwOnError* | Логическое | Да | *Только SessionStateProvider*<br/>*Этот атрибут может быть указан только с помощью web.config или AppSettings.*<br/><br/>Нужно ли создавать исключение при возникновении ошибки.<br/><br/>Дополнительные сведения о *throwOnError*, см. в разделе [примечания о *throwOnError* ](#notes-on-throwonerror) в [атрибут заметки](#attribute-notes) раздел. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | положительное целое число | 5000 | *Только SessionStateProvider*<br/>*Этот атрибут может быть указан только с помощью web.config или AppSettings.*<br/><br/>Как долго следует повторить, если произошел сбой операции. Если это значение меньше, чем *operationTimeoutInMilliseconds*, поставщик не будет повторять.<br/><br/>Дополнительные сведения о *retryTimeoutInMilliseconds*, см. в разделе [примечания о *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) в [атрибут заметки](#attribute-notes) раздел. |
| *redisSerializerType* | string | *н/д* | Указывает имя типа с указанием сборки, класса, реализующий Microsoft.Web.Redis. ISerializer и содержащий пользовательскую логику для сериализации и десериализации значений. Дополнительные сведения см. в разделе [о *redisSerializerType* ](#about-redisserializertype) в [атрибут заметки](#attribute-notes) раздел. |
|

## <a name="attribute-notes"></a>Атрибут заметки

### <a name="setting-connectionstring"></a>Параметр *connectionString*

Значение *connectionString* используется как ключ для получения фактической строкой подключения из AppSettings, если такая строка существует в AppSettings. Если объект не найден в AppSettings, значение *connectionString* будет использоваться в качестве ключа для получения фактической строкой подключения из файла web.config **ConnectionString** , если раздел существует, раздел. Если строка подключения не существует в AppSettings либо в файле web.config **ConnectionString** разделе литеральное значение *connectionString* будет использоваться в качестве строки подключения, при создании StackExchange.Redis.ConnectionMultiplexer.

В следующих примерах показаны как *connectionString* используется.

#### <a name="example-1"></a>Пример 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

В `web.config`, использовать выше ключ в качестве значения параметра вместо фактического значения.

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

В `web.config`, использовать выше ключ в качестве значения параметра вместо фактического значения.

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

### <a name="notes-on-throwonerror"></a>Примечания о *throwOnError*

В настоящее время при возникновении ошибки во время операции сеанса, поставщик состояний сеансов вызовет исключение. Это завершает работу приложения.

Это поведение был изменен способом, который поддерживает ожиданий существующих пользователей поставщика состояния сеанса ASP.NET, а также предоставляет возможность отреагировать на исключения, при необходимости. Поведение по умолчанию по-прежнему создает исключение при возникновении ошибки, согласованное с другими поставщиками состояния сеансов ASP.NET; существующий код должен работать теми же самыми.

Если задать *throwOnError* для **false**, а не вызывает исключение при возникновении ошибки, произойдет сбой без вмешательства пользователя. См. в разделе, если произошла ошибка, и, если да, обнаруживать, исключение, проверьте свойство *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Примечания о *retryTimeoutInMilliseconds*

Это обеспечивает определенную логику повторных попыток для упрощения случая, где некоторые операции сеанса следует повторить попытку в случае сбоя из-за таких вещей, как сбой сети, также дает возможность управлять временем ожидания повторных попыток или полностью отказаться повторных попыток.

Если задать *retryTimeoutInMilliseconds* число, например 2000, затем при сбое операции сеанса, он будет предприниматься повторная попытка 2000 мс перед рассматривая его как ошибку. Поэтому чтобы использовать поставщик состояний сеансов, чтобы применить эту логику повторных попыток, достаточно настройте время ожидания. Первая повторная попытка будет выполнена через 20 миллисекунд, которой вполне достаточно в большинстве случаев, когда происходит сбой сети. После этого будет выполняться Повтор каждую секунду до истечения времени ожидания. Сразу же после тайм-аут оно повторит попытку еще раз, чтобы убедиться, что он не обрезаны время ожидания (максимум) за одну секунду.

Если вы не считаете повторить (например, при запуске сервера Redis на том же компьютере, что и ваше приложение) или если вы хотите обработать логику повторных попыток вручную, необходимо настроить *retryTimeoutInMilliseconds* 0.

### <a name="about-redisserializertype"></a>О *redisSerializerType*

По умолчанию выполняется сериализации для хранения значений в Redis в двоичном формате, предоставляемые **BinaryFormatter** класса. Используйте *redisSerializerType* чтобы указать имя типа с указанием сборки, класса, реализующего **Microsoft.Web.Redis.ISerializer** и имеет пользовательскую логику для сериализации и десериализации значений. Например вот класс сериализатора Json, с помощью JSON.NET:

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
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

При условии, что этот класс определен в сборке с именем **MyCompanyDll**, можно задать параметр *redisSerializerType* для его использования:

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

В предыдущем примере кэшированные данные страницы остаются в кэше в течение 60 секунд, а для каждой комбинации параметров кэшируется другая версия страницы. Для получения дополнительной информации о директиве OutputCache ознакомьтесь с [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Когда эти действия будут выполнены, приложение будет соответствующим образом настроено и сможет использовать поставщик кэша вывода Redis.

## <a name="next-steps"></a>Дальнейшие действия

Посетите страницу [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) (Поставщик состояний сеансов ASP.NET для кэша Azure для Redis).

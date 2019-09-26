---
title: Перенос приложений управляемой службы кэша в Redis в Azure | Документация Майкрософт
description: Узнайте, как перенести приложения управляемой службы кэша и приложения кэша роли в кэш Azure для Redis.
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 05638e17c2f41806a5c8aa3e0c3020eae82bdb60
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315965"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Перенос из управляемой службы кэша в кэш Azure для Redis
Перенести приложения, которые используют управляемую службу кэша Azure, в кэш Azure для Redis можно, не внося в приложения существенных изменений. Объем изменений зависит от того, какие функции управляемой службы кэша использует ваше приложение кэширования. Хотя интерфейсы API не идентичны, они похожи, поэтому большую часть вашего кода, использующего управляемую службу кэша для доступа к кэшу, можно использовать повторно с минимальными изменениями. В этой статье показано, как внести необходимые изменения в конфигурацию и приложения, чтобы перенести приложения управляемой службы кэша для использования кэша Azure для Redis. Здесь также объясняется, как с помощью некоторых функций кэша Azure для Redis реализовать функции кэша управляемой службы кэша.

>[!NOTE]
>Использование управляемой службы кэша и кэша роли [прекращено](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 ноября 2016 г. Если у вас есть развертывания кэша роли, которые нужно перенести в кэш Azure для Redis, выполните описанные в этой статье действия.

## <a name="migration-steps"></a>Этапы миграции
Чтобы перенести приложение управляемой службы кэша для использования кэша Azure для Redis, выполните приведенные ниже действия.

* Сопоставление управляемой службы кэша с кэшем Azure для Redis.
* Выбор уровня кэша.
* Создание кэша.
* Настройка клиентов кэша.
  * Удаление конфигурации управляемой службы кэша
  * Настройка клиента кэша с помощью пакета NuGet для StackExchange.Redis.
* Перенос кода управляемой службы кэша.
  * Подключение к кэшу с помощью класса ConnectionMultiplexer
  * Доступ к несложным типам данных в кэше.
  * Работа с объектами .NET в кэше
* Перенос состояний сеансов ASP.NET и кэширование выводимых данных в кэш Azure для Redis. 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Сопоставление управляемой службы кэша с кэшем Azure для Redis.
Управляемая служба кэша Azure и кэш Azure для Redis похожи, но некоторые их функции реализуются по-разному. В этом разделе описаны некоторые различия и приведены рекомендации по реализации функций управляемой службы кэша в кэше Azure для Redis.

| Функция управляемой службы кэша | Поддержка управляемой службы кэша | Поддержка кэша Azure для Redis |
| --- | --- | --- |
| Именованные кэши |Настраивается кэш по умолчанию. При этом в ценовых уровнях «Стандартный» и «Премиум» можно при необходимости настроить до 9 дополнительных именованных кэшей. |Кэш Azure для Redis может использовать настраиваемое количество баз данных (по умолчанию — до 16) для реализации похожих функций в именованных кэшах. Дополнительные сведения см. в разделах [What are Redis databases?](cache-configure.md#default-redis-server-configuration) (Что такое базы данных Redis) и [Конфигурация сервера Redis по умолчанию](cache-faq.md#what-are-redis-databases). |
| Высокая доступность |Обеспечивает высокую доступность для элементов в кэше в предложениях кэша «Стандартный» и «Премиум». Если вследствие сбоя элементы утрачиваются, резервные копии элементов в кэше остаются доступными. Несколько процессов записи во вторичный кэш протекают синхронно. |Высокая доступность предоставляется в предложениях кэша «Стандартный» и «Премиум», в которых используется двухузловая конфигурация «Первичный кэш и реплика» (каждому сегменту в кэше «Премиум» соответствует пара «первичный кэш — реплика»). Несколько процессов записи в реплику протекают асинхронно. Дополнительные сведения см. на странице [цен кэша Azure для Redis](https://azure.microsoft.com/pricing/details/cache/). |
| Уведомления |Позволяет клиентам получать асинхронные уведомления, когда в именованном кэше выполняется множество операций. |Клиентские приложения могут использовать публикацию и подписку Redis либо [уведомления ключей](cache-configure.md#keyspace-notifications-advanced-settings) для реализации подобной функциональности в уведомлениях. |
| Локальный кэш |Сохраняет копии кэшированных объектов локально на клиенте для очень быстрого доступа к ним. |Клиентским приложениям нужно реализовывать эту функцию с помощью словаря или аналогичной структуры данных. |
| Политика вытеснения |Нет или наиболее давно использовавшаяся. По умолчанию задана наиболее давно использовавшаяся. |Кэш Azure для Redis поддерживает следующие политики вытеснения: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl и noeviction. Политика по умолчанию — volatile-lru. Дополнительные сведения см. в разделе [Конфигурация сервера Redis по умолчанию](cache-configure.md#default-redis-server-configuration). |
| Политика срока действия |По умолчанию используется политика Absolute, а интервал истечения срока действия составляет 10 минут. Доступны также политики Sliding и Never. |По умолчанию срок действия элементов в кэше не ограничен, но его можно ограничить для каждого события записи с помощью перегрузок набора кэшей. |
| Регионы и добавление тегов |Регионы являются подгруппами для кэшированных элементов. Регионы также поддерживают добавление к кэшированным элементам дополнительных описательных строк, которые называются тегами. В рамках региона можно выполнять поиск любого элемента, который помечен тегом. Все элементы в регионе находятся в одном узле кластера кэша. |Кэш Azure для Redis состоит из одного узла (если не включен кластер Redis), поэтому концепция регионов управляемой службы кэша не применяется. Когда извлекаются ключи, Redis поддерживает поиск и операции с подстановочными знаками. Поэтому описательные теги можно внедрить в имена ключей, а затем использовать эти теги для извлечения элементов. Пример добавления тегов с помощью Redis см. в статье [Добавление тегов с помощью Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Сериализация |Управляемый кэш поддерживает сериализаторы NetDataContractSerializer, BinaryFormatter, а также настраиваемые сериализаторы. По умолчанию используется NetDataContractSerializer. |Клиентское приложение отвечает за сериализацию объектов .NET перед их размещением в кэш. При этом сериализаторы выбирает разработчик клиентского приложения. Дополнительную информацию и пример кода см. в разделе [Работа с объектами .NET в кэше](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Эмулятор кэша |Управляемый кэш предоставляет локальный эмулятор кэша. |В кэше Azure для Redis нет эмулятора, но вы можете [локально запустить сборку MSOpenTech redis-server.exe](cache-faq.md#cache-emulator), чтобы получить функции эмулятора. |

## <a name="choose-a-cache-offering"></a>Выбор уровня кэша.
Кэш Microsoft Azure для Redis доступен на следующих уровнях:

* **Basic** — один узел. Множество размеров вплоть до 53 ГБ.
* **Стандартный** — два узла: основной и реплика. Множество размеров вплоть до 53 ГБ. СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ 99,9 %.
* **Премиум** — два узла (основной и реплика), включающие до 10 сегментов. Несколько размеров от 6 ГБ до 1,2 ТБ. Все функции уровня "Стандартный", а также дополнительные функции, включая поддержку [кластера Redis](cache-how-to-premium-clustering.md), [сохраняемости Redis](cache-how-to-premium-persistence.md) и [виртуальной сети Azure](cache-how-to-premium-vnet.md). СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ 99,9 %.

Каждый уровень отличается доступными возможностями и ценой. Конкретные возможности рассматриваются ниже в этом руководстве, а дополнительные сведения о ценах доступны на странице [Сведения о ценах — кэш](https://azure.microsoft.com/pricing/details/cache/).

В начале переноса нужно выбрать размер, соответствующий размеру предыдущего кэша управляемой службы кэша, а затем увеличить или уменьшить масштаб в соответствии с требованиями вашего приложения. Дополнительные сведения по правильному выбору предложения кэша Azure для Redis см. в разделе [Какое предложение и размер кэша Azure для Redis мне следует использовать?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="create-a-cache"></a>Создание кэша
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Настройка клиентов кэша.
После создания и настройки кэша нужно удалить конфигурацию управляемой службы кэша и добавить конфигурацию кэша Azure для Redis и ссылки, чтобы клиенты кэша могли обращаться к кэшу.

* Удаление конфигурации управляемой службы кэша
* Настройка клиента кэша с помощью пакета NuGet для StackExchange.Redis.

### <a name="remove-the-managed-cache-service-configuration"></a>Удаление конфигурации управляемой службы кэша
Прежде чем настраивать клиентские приложения для кэша Azure для Redis, нужно удалить существующие ссылки на сборку, а также конфигурацию управляемой службы кэша. Для этого нужно удалить пакет NuGet управляемой службы кэша.

Для удаления клиентского приложения с помощью пакета NuGet управляемой службы кэша щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. Выберите узел **Установленные пакеты** и введите **WindowsAzure.Caching** в поле "Search installed packages" (Поиск установленных пакетов). В зависимости от версии пакета NuGet выберите **Windows** **Azure Cache** или **Windows** **Azure Caching**. Затем последовательно щелкните **Удалить** и **Закрыть**.

![Удаление пакета NuGet управляемой службы кэша Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Удаление пакета NuGet управляемой службы кэша приводит к удалению сборок и записей управляемой службы кэша в файле app.config или web.config клиентского приложения. Так как некоторые пользовательские параметры нельзя удалить одновременно с пакетом NuGet, откройте файл web.config или app.config и убедитесь, что следующие элементы удалены.

Убедитесь, что запись `dataCacheClients` удалена из элемента `configSections`. Не удаляйте весь элемент `configSections`. Просто удалите запись `dataCacheClients`, если она есть.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Убедитесь, что раздел `dataCacheClients` удален. Раздел `dataCacheClients` будет похож на следующий пример.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

После удаления конфигурации управляемой службы кэша вы можете настроить клиент кэша в соответствии с описаниями, приведенными в следующем разделе.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Настройка клиента кэша с помощью пакета NuGet для StackExchange.Redis.
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Перенос кода управляемой службы кэша.
Интерфейс API для клиента кэша StackExchange.Azure для Redis аналогичен управляемой службе кэша. В этом разделе содержится обзор различий.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Подключение к кэшу с помощью класса ConnectionMultiplexer
В управляемой службе кэша подключения к кэшу обрабатывались классами `DataCacheFactory` и `DataCache`. В кэше Azure для Redis этими подключениями управляет класс `ConnectionMultiplexer`.

Добавьте следующий оператор using в начало любого файла, из которого вы хотите получить доступ к кэшу.

```csharp
using StackExchange.Redis
```

Если это пространство имен не разрешается, убедитесь, что вы добавили пакет NuGet для StackExchange.Redis в соответствии с описаниями, приведенными в статье [Краткое руководство. Использование кэша Azure для Redis с приложениями .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Обратите внимание, что клиенту StackExchange.Redis необходима среда .NET Framework 4 или выше.
> 
> 

Чтобы подключиться к экземпляру кэша Azure для Redis, вызовите статический метод `ConnectionMultiplexer.Connect` и передайте конечную точку и ключ. Один из способов совместного использования экземпляра `ConnectionMultiplexer` в приложении предполагает наличие статического свойства, которое возвращает подключенный экземпляр (как в приведенном ниже примере). Этот подход помогает потокобезопасно инициализировать отдельный подключенный экземпляр `ConnectionMultiplexer`. В этих примерах для параметра `abortConnect` задано значение False, что указывает на безопасное завершение вызова, даже если подключение к кэшу не установлено. Одна из ключевых особенностей `ConnectionMultiplexer` заключается в том, что этот параметр автоматически восстанавливает соединение с кэшем, когда устраняется проблема с сетью или другие проблемы.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Конечная точка кэша, ключи и порты указаны в колонке **Кэш Azure для Redis** вашего экземпляра кэша. Дополнительные сведения см. в разделе о [свойствах кэша Azure для Redis](cache-configure.md#properties).

Создав подключение, верните ссылку на базу данных кэша Azure для Redis с помощью метода `ConnectionMultiplexer.GetDatabase`. Объект, возвращенный из метода `GetDatabase`, – это упрощенный передаваемый объект, не требующий сохранения.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Клиент StackExchange.Redis использует типы `RedisKey` и `RedisValue` для доступа к элементам в кэше и хранения их. Эти типы сопоставляются с большинством несложных типов языков, например со строками, и часто не используются напрямую. Строки Redis — это самый базовый тип значения Redis. Он может содержать много типов данных, в том числе сериализованные двоичные потоки. Возможно, вы не будете использовать этот тип напрямую, а будете использовать методы, содержащие `String` в имени. Работая с большинством примитивных типов данных, хранить элементы в кэше и извлекать их из него вы будете с помощью методов `StringSet` и `StringGet` (если только вы не храните в кэше коллекции или другие типы данных Redis). 

Методы `StringSet` и `StringGet` похожи на методы `Put` и `Get` управляемой службы кэша. Главное различие между ними таково: прежде чем задать и поместить объект .NET в кэш, нужно его сериализовать. 

Если объект существует, метод `StringGet` возвращает его; в противном случае возвращается значение «null». В этом случае можно извлечь значение из желаемого источника данных и сохранить его в кэше для последующего использования. Это называется шаблоном "кэш на стороне".

Чтобы указать срок действия объекта в кэше, используйте параметр `TimeSpan` метода `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Кэш Azure для Redis может работать с объектами .NET, а также несложными типами данных, но прежде чем объект .NET можно будет кэшировать, он должен быть сериализован. За сериализацию отвечает разработчик приложения, что дает ему свободу в выборе сериализатора. Дополнительную информацию и пример кода см. в разделе [Работа с объектами .NET в кэше](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Перенос состояний сеансов ASP.NET и кэширование выводимых данных в кэш Azure для Redis.
В кэше Azure для Redis есть поставщики состояния сеанса ASP.NET и кэширования вывода страниц. Чтобы перенести приложение, использующее те версии поставщиков, которые предназначены для управляемой службы кэша, сначала нужно удалить существующие разделы из файла web.config, а затем настроить версии поставщиков, которые использует кэш Azure для Redis. Инструкции по использованию поставщиков ASP.NET кэша Azure для Redis см. в статьях [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) (Поставщик состояний сеансов ASP.NET для кэша Azure для Redis) и [ASP.NET Output Cache Provider for Azure Cache for Redis](cache-aspnet-output-cache-provider.md) (Поставщик кэша вывода ASP.NET для кэша Azure для Redis).

## <a name="next-steps"></a>Следующие шаги
Ознакомьтесь с руководствами, образцами и видеозаписями в [документации по кэшу Azure для Redis](https://azure.microsoft.com/documentation/services/cache/).


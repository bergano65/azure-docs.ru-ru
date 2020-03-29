---
title: Размещение статических веб-сайтов в службе хранилища Azure
description: Размещение статических веб-сайтов в службе хранилища Azure предоставляет экономичное, масштабируемое решение для размещения современных веб-приложений.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370497"
---
# <a name="static-website-hosting-in-azure-storage"></a>Размещение статических веб-сайтов в службе хранилища Azure

Вы можете обслуживать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера для хранения данных под названием *$web.* Хостинг содержимого в Хранилище Azure позволяет использовать бессерверные архитектуры, которые включают [функции Azure](/azure/azure-functions/functions-overview) и другие платформы в качестве службы (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Если ваш сайт зависит от кода сервера, вместо этого используйте [службу приложений Azure.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Настройка статического веб-сайта

Статический хостинг веб-сайта — это функция, которую необходимо включить в учетную запись хранения.

Для включения статического хостинга веб-сайта выберите имя файла по умолчанию, а затем по желанию предоставьте путь к пользовательской странице 404. Если контейнер для хранения каблов, названный **$web,** еще не существует в учетной записи, он создан для вас. Добавьте файлы вашего сайта в этот контейнер.

Для пошагового руководства смотрите [статический веб-сайт в Хранилище Azure.](storage-blob-static-website-how-to.md)

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Файлы в **$web** контейнере чувствительны к случаям, обслуживаются через анонимные запросы доступа и доступны только через считывательные операции.

## <a name="uploading-content"></a>Загрузка контента

Вы можете использовать любой из этих инструментов для загрузки содержимого в **$web** контейнер:

> [!div class="checklist"]
> * [Лазурный CLI](storage-blob-static-website-how-to.md#cli)
> * [модуль Azure PowerShell;](storage-blob-static-website-how-to.md#powershell)
> * [Azcopy](../common/storage-use-azcopy-v10.md)
> * [Исследователь хранения azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Расширение Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Просмотр контента

Пользователи могут просматривать содержимое сайта из браузера, используя общедоступный URL-адрес веб-сайта. Url можно найти с помощью портала Azure, Azure CLI или PowerShell. Используйте эту таблицу в качестве справочника.

|Инструмент| Руководство |
|----|----|
|**Портал Azure** | [Найти URL-адрес веб-сайта с помощью портала Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Лазурный CLI** | [Найти URL-адрес веб-сайта с помощью Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**модуль Azure PowerShell;** | [Найти URL-адрес веб-сайта с помощью PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

URL-адрес вашего сайта содержит региональный код. Например, `https://contosoblobaccount.z22.web.core.windows.net/` URL содержит `z22`региональный код.

Хотя этот код должен оставаться в URL-адресе, он предназначен только для внутреннего использования, и вам не придется использовать этот код каким-либо другим способом.

Документ индекса, который вы указываете, когда вы включите статический хостинг веб-сайта, `https://contosoblobaccount.z22.web.core.windows.net`появляется, когда пользователи открывают сайт и не указывают конкретный файл (например: ).  

Если сервер возвращает ошибку 404 и вы не указали документ об ошибке при включении веб-сайта, пользователь возвращает страницу по умолчанию 404.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) не поддерживается статическим веб-сайтом.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Влияние установления уровня общего доступа в веб-контейнер

Вы можете изменить уровень общедоступного доступа **$web** контейнера, но это не влияет на основную статическую конечную точку веб-сайта, поскольку эти файлы подаются через анонимные запросы доступа. Это означает открытый (только для чтения) доступ ко всем файлам.

На следующем скриншоте показан атакжем общественного уровня доступа на портале Azure:

![Скриншот, показывающий, как установить уровень общественного доступа на портале](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Хотя основная статическая конечная точка веб-сайта не затронута, изменение уровня общественного доступа действительно влияет на конечную точку первичного обслуживания капли.

Например, если вы измените уровень общего доступа **$web** контейнера с **Частного (без анонимного доступа)** на **Blob (анонимный доступ читать только для капли)**, то уровень общего доступа к основной статической конечной точке `https://contosoblobaccount.z22.web.core.windows.net/index.html` веб-сайта не меняется.

Тем не менее, общественный доступ к `https://contosoblobaccount.blob.core.windows.net/$web/index.html` основной конечная точка службы капли действительно меняется от частного к государственному. Теперь пользователи могут открыть этот файл, используя одну из этих двух конечных точек.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Отображение пользовательского домена на url-адрес статического веб-сайта

Вы можете сделать свой статический веб-сайт доступным через пользовательский домен. 

Проще включить http доступ для пользовательского домена, так как Azure Storage нативно поддерживает его. Для включения HTTPS вам придется использовать Azure CDN, поскольку Azure Storage еще не поддерживает HTTPS с пользовательскими доменами. Для поэтапного руководства [отнесена карта пользовательского домена в конечную точку хранилища Azure Blob Storage.](storage-custom-domain-name.md)

Если учетная запись хранилища настроена [на безопасную передачу](../common/storage-require-secure-transfer.md) по HTTPS, пользователи должны использовать конечную точку HTTPS. 

> [!TIP]
> Рассмотрите возможность размещения домена в Azure. Для получения дополнительной информации [смотрите, как размещайте свой домен в Azure DNS.](../../dns/dns-delegate-domain-azure-dns.md)

## <a name="adding-http-headers"></a>Добавление заголовков HTTP

Нет никакого способа настроить заголовки как часть функции статического веб-сайта. Тем не менее, можно использовать Azure CDN для добавления заголовков и значений заголовков (или перезаписи) заголовков. Смотрите [ссылку на движки стандартных правил для Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Если требуется использовать заголовки для управления кэшированием, [см.](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)

## <a name="pricing"></a>Цены

Вы можете включить статический веб-сайт хостинг бесплатно. Вы выставляете счет только для хранения капли, что ваш сайт использует и эксплуатационные расходы. Дополнительные сведения о ценах на хранилище BLOB-объектов Azure см. на [странице цен](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Метрики

Вы можете включить метрики на статических страницах веб-сайта. После включения метрик статистика трафика файлов в **$web** контейнере сообщается в панели мониторинга метрик.

Для включения метрик на статических страницах веб-сайта [см.](storage-blob-static-website-how-to.md#metrics)

## <a name="next-steps"></a>Дальнейшие действия

* [Размещение статического веб-сайта в Хранилище Azure](storage-blob-static-website-how-to.md)
* [Map a custom domain to an Azure Blob Storage endpoint](storage-custom-domain-name.md) (Сопоставление личного домена с конечной точкой хранилища BLOB-объектов Azure)
* [Проверка](/azure/azure-functions/functions-overview)
* [Служба приложений Azure](/azure/app-service/overview)
* [Создание первого бессерверного веб-приложения](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Руководство. Размещение домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)

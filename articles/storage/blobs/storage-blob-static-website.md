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
ms.openlocfilehash: a35239354d23f75361d5577d6b7efc8254943147
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906592"
---
# <a name="static-website-hosting-in-azure-storage"></a>Размещение статических веб-сайтов в службе хранилища Azure

Вы можете обрабатывать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера хранилища с именем *$Web*. Размещение содержимого в службе хранилища Azure позволяет использовать бессерверные архитектуры, включающие [функции Azure](/azure/azure-functions/functions-overview) и другие службы "платформа как услуга" (PaaS).

> [!NOTE]
> Если ваш сайт зависит от кода на стороне сервера, используйте вместо него [службу приложений Azure](/azure/app-service/overview) .

## <a name="setting-up-a-static-website"></a>Настройка статического веб-сайта

Размещение статических веб-сайтов — это функция, которую необходимо включить в учетной записи хранения.

Чтобы включить размещение статических веб-сайтов, выберите имя файла по умолчанию, а затем при необходимости укажите путь к пользовательской странице 404. Если контейнер хранилища BLOB-объектов с именем **$Web** еще не существует в учетной записи, он будет создан автоматически. Добавьте файлы сайта в этот контейнер.

Пошаговые инструкции см. [в разделе размещение статического веб-сайта в службе хранилища Azure](storage-blob-static-website-how-to.md).

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Файлы в контейнере **$Web** чувствительны к регистру, обрабатываются через анонимные запросы и доступны только через операции чтения.

## <a name="uploading-content"></a>Отправка содержимого

Вы можете использовать любое из этих средств для отправки содержимого в контейнер **$Web** :

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Overview of Azure PowerShell](storage-blob-static-website-how-to.md#powershell) (Общие сведения об Azure PowerShell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Расширение Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Просмотр содержимого

Пользователи могут просматривать содержимое сайта в браузере с помощью общедоступного URL-адреса веб – сайта. URL-адрес можно найти с помощью портал Azure, Azure CLI или PowerShell. Используйте эту таблицу в качестве справочника.

|Средство| Руководство |
|----|----|
|**Портал Azure** | [Поиск URL-адреса веб-сайта с помощью портал Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Поиск URL-адреса веб-сайта с помощью Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Overview of Azure PowerShell** (Общие сведения об Azure PowerShell) | [Поиск URL-адреса веб-сайта с помощью PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

URL-адрес сайта содержит региональный код. Например, URL-адрес `https://contosoblobaccount.z22.web.core.windows.net/` содержит региональные `z22`кода.

Хотя этот код должен оставаться в URL-адресе, он предназначен только для внутреннего использования, и вам не придется использовать этот код другим способом.

Документ индекса, указанный при включении размещения статических веб-сайтов, отображается, когда пользователи открывают сайт и не указывают конкретный файл (например, `https://contosoblobaccount.z22.web.core.windows.net`).  

Если сервер возвращает ошибку 404 и при включении веб-сайта не был указан документ об ошибке, то пользователю возвращается страница 404 по умолчанию.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) не поддерживается на статическом веб-сайте.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Влияние параметра на общий уровень доступа веб-контейнера

Можно изменить уровень общего доступа контейнера **$Web** , но это не повлияет на конечную точку основной статической веб-сайта, так как эти файлы обслуживаются через запросы анонимного доступа. Это означает доступ ко всем файлам — Public (только для чтения).

На следующем снимке экрана показан параметр уровня общего доступа в портал Azure.

![Снимок экрана, показывающий, как задать общий уровень доступа на портале](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Хотя основная конечная точка веб-сайта не затрагивается, изменение общедоступного уровня доступа влияет на конечную точку основной службы больших двоичных объектов.

Например, если изменить уровень общего доступа **$Web** контейнера с **Private (без анонимного доступа)** на **BLOB-объект (анонимный доступ на чтение только для больших двоичных объектов)** , то уровень общего доступа к основной конечной точке веб-сайта `https://contosoblobaccount.z22.web.core.windows.net/index.html` не изменится.

Однако открытый доступ к основной конечной точке службы BLOB-объектов `https://contosoblobaccount.blob.core.windows.net/$web/index.html` меняется с Private на public. Теперь пользователи могут открыть этот файл с помощью любой из этих двух конечных точек.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Сопоставление пользовательского домена с URL-адресом статического веб-сайта

Статический веб-сайт можно сделать доступным через личный домен. 

Проще включить доступ по протоколу HTTP для личного домена, так как служба хранилища Azure изначально поддерживает ее. Чтобы включить протокол HTTPS, необходимо использовать Azure CDN, так как служба хранилища Azure еще не поддерживает протокол HTTPS с пользовательскими доменами. Пошаговое руководство см. в статье [сопоставьте пользовательский домен с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md) .

Если учетная запись хранения настроена на [требование безопасной передачи](../common/storage-require-secure-transfer.md) по протоколу HTTPS, пользователи должны использовать конечную точку HTTPS. 

> [!TIP]
> Рассмотрите возможность размещения домена в Azure. Дополнительные сведения см. [в разделе Размещение домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Стоимость

Можно включить бесплатное размещение статических веб-сайтов. Вы оплачиваете только хранилище BLOB-объектов, которое использует ваш сайт, и затраты на эксплуатацию. Дополнительные сведения о ценах на хранилище BLOB-объектов Azure см. на [странице цен](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Метрики

Вы можете включить метрики на статических страницах веб-сайта. После включения метрик в панели мониторинга метрик отображается статистика трафика по файлам в контейнере **$Web** .

Сведения о включении метрик на статических страницах веб-сайта см. в разделе [Включение метрик на статических страницах веб-сайта](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Дальнейшие действия

* [Размещение статического веб-сайта в службе хранилища Azure](storage-blob-static-website-how-to.md)
* [Сопоставьте личный домен с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md)
* [Функции Azure](/azure/azure-functions/functions-overview)
* [Служба приложений Azure](/azure/app-service/overview)
* [Создание первого бессерверного веб-приложения](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Руководство. Размещение домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)

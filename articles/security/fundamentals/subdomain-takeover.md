---
title: Запретить поддомену такеоверс с записями псевдонимов Azure DNS и проверкой пользовательского домена службы приложений Azure
description: Узнайте, как избежать распространенных угроз с высокой серьезностью поддоменных перенаправление
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890830"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Предотвращение висячих записей DNS и избежание поддоменного перенаправление

В этой статье описывается распространенная угроза безопасности поддоменных перенаправление и действия, которые можно предпринять для устранения проблемы.


## <a name="what-is-subdomain-takeover"></a>Что такое поддомены перенаправление?

Такеоверс поддоменов — это распространенная угроза высокой серьезности для организаций, которые регулярно создают и удаляют множество ресурсов. Дочерний домен перенаправление может возникать, если имеется запись DNS, указывающая на отозванный ресурс Azure. Такие записи DNS также называются "висячими DNS" записями. Записи CNAME особенно уязвимы для этой угрозы.

Распространенный сценарий для поддомена перенаправление:

1. Создается веб-сайт. 

    В этом примере — `app-contogreat-dev-001.azurewebsites.net`.

1. Запись CNAME добавляется в DNS, указывающую на веб-сайт. 

    В этом примере было создано следующее понятное имя: `greatapp.contoso.com` .

1. Через несколько месяцев сайт больше не нужен, поэтому он удаляется **без** удаления соответствующей записи DNS. 

    DNS-запись CNAME теперь называется "висячим".

1. Практически сразу после удаления сайта субъект угрозы обнаруживает отсутствующий сайт и создает свой собственный веб-сайт по адресу `app-contogreat-dev-001.azurewebsites.net` .

    Теперь трафик, предназначенный для, передается `greatapp.contoso.com` на сайт Azure с субъектом-службой, а субъект угрозы контролирует отображаемое содержимое. 

    Использование висячего DNS было злоумышленником, а поддомен Contoso "Греатапп" был жертвой поддомена перенаправление. 

![Дочерний домен перенаправление от отозванного веб-сайта](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Риски поддомена перенаправление

Если запись DNS указывает на недоступный ресурс, сама запись должна быть удалена из зоны DNS. Если он не был удален, это запись «висячий DNS» и создает возможность для поддомена перенаправление.

Висячие записи DNS позволяют субъектам-угрозам управлять связанным DNS-именем для размещения вредоносного веб-сайта или службы. Вредоносные страницы и службы в поддомене организации могут привести к следующим результатам:

- **Утрата контроля за содержимым поддоменом** — отрицательное воздействие Организации на невозможность защитить ее содержимое, а также ущерб торговой марки и потери доверия.

- Файл cookie, который получается **от неподозрительных посетителей** . обычно веб-приложениям предоставляется доступ к файлам cookie сеанса в поддоменах (*. contoso.com), поэтому к ним могут обращаться все поддомены. Актеры угроз могут использовать поддомен перенаправление для создания подлинной страницы, обманным путем, чтобы пользователи могли посетить эту страницу и собирать свои файлы cookie (даже безопасные файлы cookie). Распространенное заблуждение заключается в том, что использование SSL-сертификатов защищает сайт и файлы cookie пользователей от перенаправление. Однако субъект угрозы может использовать захваченный поддомен для применения и получения действительного SSL-сертификата. Действительные SSL-сертификаты предоставляют им доступ к защищенным файлам cookie и могут дополнительно повысить воспринимаемую законность вредоносного сайта.

- **Фишинговые кампании** . подлинные поддомены можно использовать в кампаниях с фишингом. Это справедливо для вредоносных веб-узлов, а также для записей MX, которые позволяют субъекту угрозы получать сообщения электронной почты, адресованные законному поддомену известной торговой марки.

- **Дополнительные риски** — вредоносные веб-сайты можно использовать для эскалации на другие классические атаки, такие как XSS, CSRF, CORS и т. д.



## <a name="preventing-dangling-dns-entries"></a>Предотвращение висячих записей DNS

Обеспечение того, что в организации реализованы процессы для предотвращения висячих DNS-записей, а результирующий такеоверс поддомена является важной частью программы безопасности.

Доступные сегодня меры по превентивной работе перечислены ниже.


### <a name="use-azure-dns-alias-records"></a>Использование записей псевдонимов Azure DNS

[Записи псевдонимов](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) Azure DNS могут предотвратить висячие ссылки, связывая жизненный цикл записи DNS с ресурсом Azure. Например, рассмотрим запись DNS, которая является псевдонимом и указывает на общедоступный IP-адрес или профиль диспетчера трафика. Если удалить эти базовые ресурсы, запись псевдонима DNS станет пустым набором записей. Он больше не ссылается на удаленный ресурс. Важно отметить, что существует ряд ограничений, которые можно защищать с помощью записей псевдонимов. Сейчас список ограничен:

- Azure Front Door
- Профили диспетчера трафика
- Конечные точки сети доставки содержимого Azure (CDN)
- Общедоступные IP-адреса.

Несмотря на использование ограниченных предложений служб, мы рекомендуем использовать записи псевдонимов для защиты от перенаправление поддоменов везде, где это возможно.

Дополнительные [сведения](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) о возможностях записей псевдонимов Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Использовать проверку пользовательского домена службы приложений Azure

При создании записей DNS для службы приложений Azure создайте АСУиД. поддомен Запись типа TXT с ИДЕНТИФИКАТОРом проверки домена. Если такая запись типа TXT существует, никакая другая подписка Azure не может проверить личный домен, который перестоит. 

Эти записи не запрещают пользователям создавать службу приложений Azure с тем же именем, что и запись CNAME. Без возможности доказательства владельца имени домена субъекты угроз не могут получить трафик или управлять содержимым.

Дополнительные [сведения](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) о сопоставлении существующего НАСТРАИВАЕМОГО DNS-имени с службой приложений Azure.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Создание и автоматизация процессов для устранения угрозы

Часто разработчики и группы операций могут запускать процессы очистки, чтобы избежать висячих угроз DNS. Приведенные ниже рекомендации помогут вашей организации избежать низкий от этой угрозы. 

- **Создание процедур для предотвращения:**

    - Обучить разработчикам приложений возможность перенаправлять адреса при удалении ресурсов.

    - Вставьте "удалить запись DNS" в списке обязательных проверок при списании службы.

    - Разместите [блокировки удаления](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) для всех ресурсов, имеющих пользовательскую запись DNS. Блокировка удаления служит индикатором того, что сопоставление должно быть удалено до отмены наполнения ресурса. Такие меры могут работать только вместе с внутренними программами для образования.

- **Создание процедур для обнаружения:**

    - Регулярно просматривайте свои записи DNS, чтобы убедиться, что поддомены сопоставлены с ресурсами Azure, которые:

        - Существует. запросите зоны DNS для ресурсов, указывающих на поддомены Azure, такие как *. azurewebsites.net или *. cloudapp.azure.com (см. [этот список ссылок](azure-domains.md)).
        - Вы сами подтверждаете, что являетесь владельцем всех ресурсов, на которые нацелены поддомены DNS.

    - Настройте каталог услуг конечных точек полного доменного имени (FQDN) Azure и владельцев приложений. Чтобы создать каталог услуг, выполните следующий скрипт запроса графа ресурсов Azure. Этот сценарий проецирует сведения о конечной точке FQDN ресурсов, к которым у вас есть доступ, и выводит их в CSV-файл. Если у вас есть доступ ко всем подпискам для клиента, сценарий учитывает все эти подписки, как показано в следующем примере скрипта. Чтобы ограничить результаты конкретным набором подписок, измените скрипт, как показано ниже.

        >[!IMPORTANT]
        > **Разрешения** . Запустите запрос от имени пользователя, имеющего доступ ко всем подпискам Azure. 
        >
        > **Ограничения** . в графе ресурсов Azure есть ограничения на регулирование и разбиение на страницы, которые следует учитывать при наличии крупной среды Azure. Дополнительные [сведения](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) о работе с большими наборами данных ресурсов Azure. В следующем примере скрипта используется пакетирование подписок, чтобы избежать этих ограничений.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Список типов и их `FQDNProperty` значений, как указано в предыдущем запросе графа ресурсов:

        |Имя ресурса  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|Properties. cName|
        |хранилище BLOB-объектов Azure|microsoft.storage/storageaccounts|Properties. первичных. BLOB|
        |Azure CDN|microsoft.cdn/profiles/endpoints|Свойства. имя узла|
        |Общедоступные IP-адреса|microsoft.network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Диспетчер трафика Azure|microsoft.network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Экземпляр контейнера Azure|microsoft.containerinstance/containergroups|Properties. ipAddress. FQDN|
        |Служба управления Azure API|microsoft.apimanagement/service|Properties. Хостнамеконфигуратионс. имя_узла|
        |Служба приложений Azure|microsoft.web/sites|Properties. параметром DefaultHostName|
        |Служба приложений Azure — слоты|microsoft.web/sites/slots|Properties. параметром DefaultHostName|


- **Создание процедур для исправления:**
    - При обнаружении висячих DNS-записей команде необходимо выяснить, не произошло ли нарушение безопасности.
    - Выясните, почему адрес не был перенаправлен при списании ресурса.
    - Удалите запись DNS, если она больше не используется, или укажите правильный ресурс Azure (FQDN), принадлежащий вашей организации.
 

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о связанных службах и функциях Azure, которые можно использовать для защиты от перенаправление поддоменов, см. на следующих страницах.

- [Azure DNS поддерживает использование записей псевдонимов для пользовательских доменов](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Использовать идентификатор проверки домена при добавлении пользовательских доменов в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Краткое руководство. Выполните первый запрос графика ресурсов с помощью Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)

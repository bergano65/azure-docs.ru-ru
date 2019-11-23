---
title: Блокировка исходящего трафика из Среды службы приложений Azure
description: Руководство по интеграции с брандмауэром Azure для защиты исходящего трафика
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 936fd797786d05edd7cf0f729af33c95ad3b3c56
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405649"
---
# <a name="locking-down-an-app-service-environment"></a>Блокирование среды службы приложений

Среда Службы приложений (ССП) имеет ряд внешних зависимостей, доступ к которым необходим для правильной работы. ССП находится в пользовательской виртуальной сети Azure (VNet). Пользователи должны разрешить трафик зависимостей ССП, что является проблемой для тех пользователей, которым нужно заблокировать все выходы из своей виртуальной сети.

ССП имеет ряд входящих зависимостей. Входящий трафик управления невозможно отправлять через устройство брандмауэра. Исходные адреса этого трафика известны и опубликованы в документе [Адреса управления среды Службы приложений](https://docs.microsoft.com/azure/app-service/environment/management-addresses). Используя эту информацию можно создать группу безопасности сети, которая защитит входящий трафик.

Исходящие зависимости СПП почти полностью определены с помощью FQDN, которые не содержат статических адресов. Отсутствие статических адресов означает, что для блокировки исходящего из СПП трафика нельзя использовать группу безопасности сети. Адреса часто меняются, поэтому создать правила, основанные на текущем разрешении, и использовать их для создания NSG невозможно. 

Решение для защиты исходящих адресов заключается в использовании устройства брандмауэра, которое может контролировать исходящий трафик на основе доменных имен. Брандмауэр Azure может ограничить исходящий трафик HTTP и HTTPS на основе FQDN назначения.  

> [!NOTE]
> At this moment, we can't fully lockdown the outbound connection currently.

## <a name="system-architecture"></a>Архитектура системы

Deploying an ASE with outbound traffic going through a firewall device requires changing routes on the ASE subnet. Routes operate at an IP level. If you are not careful in defining your routes, you can force TCP reply traffic to source from another address. When your reply address is different from the address traffic was sent to, the problem is called asymmetric routing and it will break TCP.

There must be routes defined so that inbound traffic to the ASE can reply back the same way the traffic came in. Routes must be defined for inbound management requests and for inbound application requests.

The traffic to and from an ASE must abide by the following conventions

* The traffic to Azure SQL, Storage, and Event Hub are not supported with use of a firewall device. This traffic must be sent directly to those services. The way to make that happen is to configure service endpoints for those three services. 
* Route table rules must be defined that send inbound management traffic back from where it came.
* Route table rules must be defined that send inbound application traffic back from where it came. 
* All other traffic leaving the ASE can be sent to your firewall device with a route table rule.

![Сетевые подключения Среды службы приложений с Брандмауэром Azure][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Настройка брандмауэра Azure с помощью СПП 

Шаги по блокировке исходящего трафика существующей Среды службы приложений с помощью Брандмауэра Azure:

1. Включите конечные точки служб для SQL, службы хранилища и концентратора событий в подсети ASE. To enable service endpoints, go into the networking portal > subnets and select Microsoft.EventHub, Microsoft.SQL and Microsoft.Storage from the Service endpoints dropdown. Включив конечные точки служб для SQL Azure, необходимо также настроить конечные точки для всех зависимостей SQL Azure, которые есть у ваших приложений. 

   ![Выбор конечных точек служб][2]
  
1. Создайте подсеть с именем AzureFirewallSubnet в виртуальной сети, в которой находится Среда службы приложений. Следуйте указаниям, приведенным в [документации по Брандмауэру Azure](https://docs.microsoft.com/azure/firewall/), чтобы создать Брандмауэр Azure.
1. В пользовательском интерфейсе Брандмауэра Azure откройте "Правила" > "Коллекция правил приложения" и выберите "Добавить коллекцию правил приложения". Укажите имя, приоритет и в поле "Действие" установите значение "Разрешить". В области тегов полных доменных имен введите имя, задайте * в качестве исходного адреса и выберите значение "Среда службы приложений" и "Центр обновления Windows". 
   
   ![Добавление правила приложения][1]
   
1. В пользовательском интерфейсе Брандмауэра Azure откройте "Правила" > "Коллекция правил сети" и выберите "Добавить коллекцию правил сети". Укажите имя, приоритет и в поле "Действие" установите значение "Разрешить". В области "Правила" введите имя, в поле "Протокол" выберите **Любой**, в полях адресов источника и назначения укажите *, а в поле "Порты назначения" — 123. Это правило позволяет системе выполнять синхронизации часов, используя NTP. Таким же образом создайте правило для порта 12000, чтобы упростить диагностику проблем с системой.

   ![Добавление сетевого правила NTP][3]

1. Создайте таблицу маршрутов с помощью адресов управления из документации [Адреса управления среды Службы приложений]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) со следующим прыжком Интернета. Записи таблицы маршрутов необходимы для предотвращения проблем с асимметричной маршрутизацией. Добавьте маршруты для зависимостей IP-адресов, указанных ниже в зависимостях IP-адреса, со следующим прыжком Интернета. Добавьте маршрут виртуального модуля к таблице маршрутов для 0.0.0.0/0, при этом следующий прыжок должен представлять частный IP-адрес вашего Брандмауэра Azure. 

   ![Создание таблицы маршрутов][4]
   
1. Назначьте таблицу маршрутов, созданную в вашей подсети Среды службы приложений.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Развертывание Среды службы приложений за брандмауэром

Шаги по развертыванию Среды службы приложений с защитой брандмауэром аналогичны настройке существующей Среды службы приложений с Брандмауэром Azure, за исключением того, что необходимо будет создать соответствующую подсеть и выполнить предыдущие шаги. Чтобы создать Среду службы приложений в существующей подсети, необходимо [использовать шаблон Resource Manager](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Трафик приложения 

Вышеупомянутые шаги позволят вашей СПП работать без проблем. Чтобы удовлетворить потребности приложения, вам все же необходимо выполнить некоторые настройки. С приложениями в СПП, настроенными с помощью брандмауэра Azure, возникают две проблемы.  

- В Брандмауэр Azure или таблицу маршрутов необходимо добавить зависимости приложения. 
- Маршруты должны создаваться для трафика приложения, чтобы избежать проблемы с асимметричной маршрутизацией.

Если ваше приложение имеет зависимости, то их необходимо добавить в брандмауэр Azure. Создайте правила приложения, чтобы разрешать трафик протокола HTTP/HTTPS, и правила сети для всего остального. 

Если вам известен диапазон адресов, из которых будет поступать трафик запроса к приложению, то вы можете добавить его в таблицу маршрутов, назначенную вашей подсети СПП. Если диапазон адресов большой или не задан, то вы можете использовать сетевое устройство, такое как приложение Gateway, которое даст вам один адрес, который необходимо добавить в таблицу маршрутов. Дополнительные сведения о настройке шлюза приложений с подсистемой балансировки нагрузки СПП см. в статье [Интеграция среды Службы приложений с внутренней подсистемой балансировки нагрузки со шлюзом приложений Azure](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Такое использование Шлюза приложений — один из примеров того, как настроить систему. Если вы предпочтете этот вариант, вам понадобится добавить маршрут к таблице маршрутов подсети Среды службы приложений, чтобы ответный трафик, передаваемый в Шлюз приложений, поступал туда напрямую. 

## <a name="logging"></a>Ведение журнала 

Брандмауэр Azure может отправлять журналы в службу хранилища Azure, концентратор событий или в журналы Azure Monitor. Чтобы интегрировать приложение с любым поддерживаемым местом назначения, перейдите в раздел "Журналы диагностики"на портале Брандмауэра Azure и включите журналы для требуемого расположения. При интеграции с журналами Azure Monitor вы сможете увидеть ведение журналов с любым трафиком, отправляемым в Брандмауэр Azure. Чтобы просмотреть отклоняемый трафик, откройте раздел "Журналы" на портале Log Analytics и введите следующий запрос. 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrating your Azure Firewall with Azure Monitor logs is useful when first getting an application working when you are not aware of all of the application dependencies. You can learn more about Azure Monitor logs from [Analyze log data in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).
 
## <a name="dependencies"></a>Зависимости

Следующие сведения требуются, только если вы хотите настроить не Брандмауэр Azure, а другой брандмауэр. 

- В разделе "Конечные точки" должны быть указаны конечные точки для включенных служб.
- Зависимости IP-адреса задаются для трафика, не использующего протокол HTTP/HTTPS (TCP и UDP-трафик)
- В устройство брандмауэра можно добавить FQDN конечных точек для протокола HTTP/HTTPS.
- Произвольные конечные точки для HTTP/HTTPS — это зависимости, которые могут меняться вместе со средой СПП в зависимости от количества квалификаторов. 
- Зависимости Linux становятся предметом беспокойства только при развертывании Linux-приложений в СПП. Если вы не развертываете Linux-приложения в СПП, то вам не обязательно добавлять эти адреса в брандмауэр. 

#### <a name="service-endpoint-capable-dependencies"></a>Зависимости конечной точки службы 

| Конечная точка |
|----------|
| Azure SQL |
| Служба хранилища Azure |
| Центры событий Azure; |

#### <a name="ip-address-dependencies"></a>Зависимости IP-адреса

| Конечная точка | Сведения |
|----------| ----- |
| \*:123 | Проверка часов NTP. Трафик проверяется в нескольких конечных точках на порте 123. |
| \*:12000 | Этот порт используется для некоторых операций мониторинга системы. If blocked, then some issues will be harder to triage but your ASE will continue to operate |
| 40.77.24.27:80 | Needed to monitor and alert on ASE problems |
| 40.77.24.27:443 | Needed to monitor and alert on ASE problems |
| 13.90.249.229:80 | Needed to monitor and alert on ASE problems |
| 13.90.249.229:443 | Needed to monitor and alert on ASE problems |
| 104.45.230.69:80 | Needed to monitor and alert on ASE problems |
| 104.45.230.69:443 | Needed to monitor and alert on ASE problems |
| 13.82.184.151:80 | Needed to monitor and alert on ASE problems |
| 13.82.184.151:443 | Needed to monitor and alert on ASE problems |

При подключении Брандмауэра Azure все перечисленные ниже параметры автоматически настраиваются с тегами полных доменных имен. 

#### <a name="fqdn-httphttps-dependencies"></a>Зависимости FQDN протокола HTTP/HTTPS 

| Конечная точка |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Зависимости подстановочного знака протокола HTTP/HTTPS 

| Конечная точка |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |

#### <a name="linux-dependencies"></a>Зависимости Linux 

| Конечная точка |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov dependencies

For US Gov you still need to set service endpoints for Storage, SQL and Event Hub.  You can also use Azure Firewall with the instructions earlier in this document. If you need to use your own egress firewall device, the endpoints are listed below.

| Конечная точка |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png

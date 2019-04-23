---
title: Адреса управления Среды службы приложений Azure
description: Список адресов управления, используемых для передачи команд в среду службы приложений
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792732"
---
# <a name="app-service-environment-management-addresses"></a>Адреса управления среды службы приложений

Среда службы приложений (ASE) — это развертывание одного клиента службы приложений Azure, который выполняется в виртуальной сети Azure (VNet).  Хотя ASE запустить в виртуальной сети, он по-прежнему необходим доступ из нескольких выделенных IP-адресов, которые используются службой приложений Azure для управления службой.  В случае ASE трафик управления передачи по сети, управляемой пользователем. Если этот трафик блокируется или неправильно маршрутизируется, работа ASE приостанавливается. Подробные сведения о зависимостях сетей ASE см. в статье [Рекомендации по работе с сетями в среде Службы приложений][networking]. Для получения общей информации о среде ASE можно начать со статьи [Общие сведения о средах службы приложений][intro].

Каждая среда ASE имеет общедоступный виртуальный IP-адрес, на который поступает трафик управления. Входящий трафик управления с этих адресов на поступает на порты 454 и 455 для общедоступного виртуального IP-адреса ASE. В этом документе перечислены адреса источников службы приложений, передающих трафик управления в среду ASE. Эти адреса также находятся в тег IP-адреса службы, с именем AppServiceManagement.

В таблицу маршрутизации, чтобы избежать асимметричного маршрутизации с трафик управления можно настроить адреса указано ниже. Маршруты реагировать на трафик на уровне IP-адрес и не имеют осведомленность о направлении трафика или трафика входит в состав ответное сообщение TCP. Если адрес ответа на запрос TCP отличается от адреса, которые он был отправлен, у вас есть асимметричного проблем с маршрутизацией. Во избежание асимметричного проблем с маршрутизацией трафика с вашего трафик управления ASE, необходимо убедиться, что ответы отправляются обратно тот же адрес, в котором они были отправлены для. Дополнительные сведения о настройке работы ASE в среде, где исходящий трафик передается локально, см. в статье [Настройка принудительного туннелирования в среде службы приложений][forcedtunnel].

## <a name="list-of-management-addresses"></a>Список адресов управления ##

| Регион | Адреса |
|--------|-----------|
| Все общедоступные регионы | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Azure для государственных организаций | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Настройка группы безопасности сети

С помощью групп безопасности сети не нужно беспокоиться о отдельные адреса или обслуживание собственной конфигурации. Есть тег IP-адрес службы, с именем AppServiceManagement, который регулярно пополняется все адреса. Чтобы использовать этот тег IP-адреса службы в группу безопасности сети, перейдите на портал, откройте пользовательский Интерфейс групп безопасности сети и выберите правила безопасности для входящего трафика. Если у вас есть существующие правила для входящего трафика управления, измените его. Если эта группа безопасности сети не была создана с использованием ASE или если она совсем новая, выберите **Добавить**. В раскрывающемся списке источника выберите **Тег службы**.  В разделе тег службы источника, выберите **AppServiceManagement**. Укажите для диапазонов портов источника значение \*, для назначения — **Любые**, для диапазонов портов назначения — **454–455**, для протокола — **TCP**, а для действия — **Разрешить**. При создании правила, необходимо задать приоритет. 

![создание группы безопасности сети с тегом службы][1]

## <a name="configuring-a-route-table"></a>Настройка таблицы маршрутов

Адреса управления можно поместить в таблицу маршрутов с типом следующего прыжка "Интернет", чтобы обеспечить возможность возвращения всего входящего трафика управления по тому же пути. Эти маршруты необходимы при настройке принудительного туннелирования. Создать таблицу маршрутов можно с помощью портала, PowerShell или Azure CLI.  Ниже приведены команды, позволяющие создать таблицу маршрутов с помощью Azure CLI из командной строки PowerShell. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

Создав свою таблицу маршрутов, необходимо задать ее для подсети ASE.  

## <a name="get-your-management-addresses-from-api"></a>Получение адресов управления из API ##

С помощью вызова API системы можно получить список адресов управления, которые соответствуют ASE.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API возвращает документ JSON, который включает все адреса входящего трафика для среды ASE. Список адресов включает адреса управления, виртуальный IP-адрес, используемый ASE и диапазон адресов подсети ASE.  

Для вызова API с помощью [armclient](https://github.com/projectkudu/ARMClient) используйте приведенные ниже команды, но укажите свои идентификатор подписки, группу ресурсов и имя среды ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md

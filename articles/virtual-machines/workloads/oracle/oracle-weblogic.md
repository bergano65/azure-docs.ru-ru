---
title: Что такое сервер Oracle Logic в Azure?
description: Сведения о том, как выполнять Oracle WebLogic Server в Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851870"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Что такое сервер Oracle Logic в Azure?

На этой странице описываются решения для запуска сервера веб-логики (WLS) на виртуальных машинах Azure.  Эти решения совместно разработаны корпорацией Oracle и корпорацией Майкрософт.

Oracle WebLogic Server стал первым в мире полностью облачным сервером приложений, который предоставляет корпоративную платформу Java для разработки и развертывания многоуровневых распределенных корпоративных приложений. Предложения сервера Azure для работы с облаком позволяют использовать облачные вычисления.  Вы получаете больший выбор и гибкость для миграции на веб – логику, включая перенос приложений Java EE в облако Azure.   WLS в Azure дает значительные последствия для небольших усилий. Предложения позволяют быстро начать работу с бизнес-приложениями.  Каждое предложение автоматически подготавливает ресурсы виртуальной сети, хранилища и Linux.  При нулевых усилиях сервер Logic устанавливается.  WLS в Azure настраивает безопасность с помощью группы безопасности сети, балансировки нагрузки с помощью шлюза приложений Azure, проверки подлинности с помощью Azure Active Directory и автоматически подключается к существующей базе данных.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Для развертывания сервера WebDeploy в Azure можно использовать портал Azure.":::

Существует четыре предложения для удовлетворения различных сценариев: один узел без сервера администрирования, один узел с сервером администрирования, кластером и динамическим кластером.  Опробуйте предложения, они доступны бесплатно.

_Все предлагаемые решения подразумевают использование собственных лицензий (BYOL)_ . Предполагается, что у вас уже есть соответствующие лицензии на Oracle и они должным образом лицензированы для запуска предложений в Microsoft Azure.

_Если вы хотите тесно работать с сценариями миграции с помощью группы инженеров, разрабатывающих эти предложения, нажмите кнопку " [связаться со мной](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) "_ в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Руководитель программы, архитекторы и инженеры будут обращаться к вам вскоре и приступить к совместной работе!

## <a name="oracle-weblogic-server-single-node"></a>Один узел Oracle WebLogic Server

Это предложение подготавливает одну виртуальную машину и устанавливает в ней WLS. Он не создает домен или не запускает сервер администрирования. Один узел полезен для сценариев с очень настраиваемой конфигурацией домена.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server с сервером администрирования

Это предложение подготавливает одну виртуальную машину и устанавливает в ней WLS. При этом создается домен и запускается сервер администрирования, который позволяет управлять этим доменом.

## <a name="oracle-weblogic-server-cluster"></a>Кластер Oracle WebLogic Server

Это предложение создает высокодоступный кластер виртуальных машин WLS. По умолчанию запускаются сервер администрирования и все управляемые серверы, позволяя вам управлять доменом.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Динамический кластер Oracle WebLogic Server

Это предложение создает высокодоступный и масштабируемый динамический кластер виртуальных машин WLS. По умолчанию запускаются сервер администрирования и все управляемые серверы, позволяя вам управлять доменом.

## <a name="next-steps"></a>Дальнейшие действия

Изучите предлагаемые решения в Azure Marketplace.

> [!div class="nextstepaction"]
> [Один узел Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server с сервером администрирования](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Кластер Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Динамический кластер Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)

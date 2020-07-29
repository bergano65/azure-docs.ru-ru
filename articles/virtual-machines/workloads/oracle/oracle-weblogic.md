---
title: Приложения Azure для Oracle WebLogic Server | Документация Майкрософт
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
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664052"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Приложения Azure для Oracle WebLogic Server

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server — это масштабируемый сервер приложений Java EE корпоративного класса.

Oracle WebLogic Server стал первым в мире полностью облачным сервером приложений, который предоставляет корпоративную платформу Java для разработки и развертывания многоуровневых распределенных корпоративных приложений. Azure WebLogic Server позволяет использовать всю мощь облачных вычислений и дает дополнительную гибкость в выборе стратегий миграции WebLogic, включая перенос приложений Java EE в облако Azure методом lift-and-shift с минимальными усилиями и максимальной эффективностью. Предлагаемые решения позволяют быстро запускать бизнес-приложения, автоматически подготавливая виртуальную сеть, хранилище и ресурсы Linux, устанавливая WebLogic Server, настраивая безопасность через группы безопасности сети и балансировку нагрузки через Шлюз приложений Azure, интегрируя проверку подлинности с Azure Active Directory и упрощая подключение к базе данных.

Предлагается четыре решения для различных сценариев: один узел без сервера администрирования, один узел с сервером администрирования, кластер и динамический кластер.  Вы можете проверить работу каждого из них, поскольку решения предоставляются бесплатно.

_Все предлагаемые решения подразумевают использование собственных лицензий (BYOL)_ . Предполагается, что вы уже приобрели все необходимые лицензии у компании Oracle и имеете все полномочия на запуск решений в Microsoft Azure.

_Если в вашем сценарии миграции нужна помощь разработчиков этих решений, просто нажмите кнопку [СВЯЖИТЕСЬ СО МНОЙ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview), чтобы руководители программ, разработчики архитектуры и инженеры могли выйти с вами на связь и наладить сотрудничество.

### <a name="oracle-weblogic-server-single-node"></a>Один узел Oracle WebLogic Server

Это решение предусматривает подготовку одной виртуальной машины и установку на ней Oracle WebLogic Server. При этом не предполагается создание домена или запуск сервера администрирования. Решение подойдет для сценариев с нестандартными конфигурациями домена.

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server с сервером администрирования

Это решение предусматривает подготовку одной виртуальной машины и установку на ней Oracle WebLogic Server. При этом создается домен и запускается сервер администрирования, который позволяет управлять этим доменом.

### <a name="oracle-weblogic-server-cluster"></a>Кластер Oracle WebLogic Server

Этот вариант предполагает создание высокодоступного кластера виртуальных машин Oracle WebLogic Server. По умолчанию запускаются сервер администрирования и все управляемые серверы, позволяя вам управлять доменом.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Динамический кластер Oracle WebLogic Server

Это решение предусматривает создание высокодоступного и хорошо масштабируемого динамического кластера виртуальных машин Oracle WebLogic Server. По умолчанию запускаются сервер администрирования и все управляемые серверы, позволяя вам управлять доменом.

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

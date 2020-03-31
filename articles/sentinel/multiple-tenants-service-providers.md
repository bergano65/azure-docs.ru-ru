---
title: Работа с несколькими арендаторами в Azure Sentinel для поставщиков услуг MSSP Документы Майкрософт
description: Как работать с несколькими арендаторами в Azure Sentinel для поставщиков услуг MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476021"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Работа с несколькими арендаторами в Azure Sentinel 

Если вы являетесь поставщиком управляемых услуг безопасности (MSSP) и используете [Azure Lighthouse](../lighthouse/overview.md) для управления центрами операций безопасности своих клиентов (SOC), вы сможете управлять ресурсами Azure Sentinel своих клиентов, не подключившись непосредственно к арендатору клиента, от собственного арендатора Azure. 

## <a name="prerequisites"></a>Предварительные требования
- [На борту Лазурного маяка](../lighthouse/how-to/onboard-customer.md)
- Для того чтобы это работало должным образом, ваш арендатор должен быть зарегистрирован в поставщике ресурсов Azure Sentinel по крайней мере по одной подписке. Если у вас есть зарегистрированный Azure Sentinel в вашем арендаторе, вы готовы приступить к работе. Если нет, выберите **Подписки** на портале Azure, за которым и **поставщики ресурсов.**  Затем, от **SOC - Ресурс провайдеров** экран, поиск и выбор `Microsoft.OperationalInsights` и, `Microsoft.SecurityInsights`и выбрать **Регистр**.
   ![Проверка поставщиков ресурсов](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Как получить доступ к Azure Sentinel от других арендаторов
1. В соответствии с **подпиской каталога и подпиской,** в которой расположены рабочие места Azure Sentinel вашего клиента.

   ![Создание инцидентов безопасности](media/multiple-tenants-service-providers/directory-subscription.png)

1. Открыть Azure Sentinel. Вы увидите все рабочие области в выбранных подписках, и вы сможете работать с ними бесшовно, как и любое рабочее пространство в вашем собственном арендаторе.

> [!NOTE]
> Вы не сможете развертывать разъемы в Azure Sentinel из управляемого рабочего пространства. Чтобы развернуть разъем, необходимо непосредственно войти в арендатора, на котором необходимо развернуть разъем и проверить подлинность с требуемыми разрешениями.





## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как беспрепятственно управлять несколькими арендаторами Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).


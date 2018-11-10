---
title: Подключение шлюза приложений Microsoft Azure к центру безопасности Azure | Документация Майкрософт
description: Узнайте, как интегрировать шлюз приложений с центром безопасности Azure, чтобы повысить общий уровень безопасности ваших ресурсов.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: acb010b0169fd876bf540f7a4115dbabfeda923a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227201"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Подключение шлюза приложений Microsoft Azure к центру безопасности Azure
В этом документе объясняется, как настроить интеграцию брандмауэра веб-приложения (WAF) шлюза приложений с центром безопасности.

## <a name="why-connect-application-gateway"></a>Зачем подключать шлюз приложений
WAF в шлюзе приложений защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и перехваты сеансов. Центр безопасности интегрируется со шлюзом приложений, чтобы предотвращать и обнаруживать угрозы, направленные на незащищенные веб-приложения в вашей среде.

## <a name="how-do-i-configure-this-integration"></a>Как настроить эту интеграцию?
Центр безопасности обнаруживает ранее развернутые экземпляры WAF в подписке. Подключите эти решения к центру безопасности, чтобы обеспечить интеграцию предупреждений.

> [!NOTE]
> WAF шлюза приложений также можно подготовить с помощью **рекомендаций** для центра безопасности, которые содержатся в статье [Добавление брандмауэра веб-приложения в Центре безопасности Azure](security-center-add-web-application-firewall.md).
>
>

1. Войдите на [портал Azure](https://azure.microsoft.com/features/azure-portal/).

2. В меню **Microsoft Azure** выберите пункт **Центр безопасности**.

3. В разделе **Гигиена безопасности ресурсов** выберите **Решения безопасности**.

  ![Обзор центра безопасности](./media/security-center-connect-application-gateway/overview.png)

4. В разделе **Обнаруженные решения** найдите брандмауэр веб-приложения Майкрософт на основе модели SaaS и нажмите кнопку **Подключить**.

  ![Обнаруженные решения](./media/security-center-connect-application-gateway/connect.png)

5. Откроется окно **Подключение решения WAF**.  Нажмите кнопку **Подключить**, чтобы интегрировать WAF с центром безопасности.

  ![Подключение решения WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как интегрировать WAF шлюза приложений в центре безопасности. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Интеграция решений по обеспечению безопасности в центре безопасности Azure](security-center-partner-integration.md)
* [Подключение Microsoft Advanced Threat Analytics к центру безопасности Azure](security-center-ata-integration.md)
* [Подключение службы "Защита идентификации Azure Active Directory" к центру безопасности Azure](security-center-aadip-integration.md)
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md).
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md).
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md).
* [Блог по безопасности Azure](https://blogs.msdn.com/b/azuresecurity/).

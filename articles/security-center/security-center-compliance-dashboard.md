---
title: Руководство по Проверки соответствия нормативным требованиям — Центр безопасности Azure
description: Руководство по Узнайте, как улучшить соответствие нормативным требованиям с помощью Центра безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 01c0f1199ab8c376fe5bb5e864f81a2730879ed1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112319"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Руководство по Обеспечение соответствия нормативным требованиям на высоком уровне
---

Центр безопасности Azure помогает упростить процесс обеспечения соответствия нормативным требованиям с помощью **специальной панели мониторинга**. На этой панели мониторинга Центр безопасности предоставляет ценные сведения об уровне соответствия на основе непрерывной оценки среды Azure. Центр безопасности анализирует факторы риска в гибридной облачной среде в соответствии с рекомендациями по безопасности. Эти оценки сопоставляются с элементами управления соответствия из поддерживаемого набора стандартов. На панели мониторинга соответствия нормативным требованиям можно просмотреть состояние всех оценок в своей среде в контексте определенного стандарта или норматива. Выполняя рекомендации и уменьшая факторы риска в вашей среде, вы сможете улучшить уровень соответствия.

Из этого учебника вы узнаете следующее:

-   Оценка соответствия нормативным требованиям с помощью специальной панели мониторинга.

-   Улучшение уровня соответствия благодаря выполнению рекомендаций.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций этого руководства требуется Центр безопасности ценовой категории "Стандартный". Вы можете бесплатно опробовать центр безопасности ценовой категории "Стандартный".
Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/security-center/). Следуйте инструкциям в [кратком руководстве по центру безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started), чтобы обновить ценовую категорию до уровня "Стандартный".

##  <a name="assess-your-regulatory-compliance"></a>Доступ к соответствию нормативным требованиям

Центр безопасности непрерывно оценивает конфигурацию ресурсов, чтобы выявить проблемы безопасности и уязвимости. Эти оценки представлены в виде рекомендаций, которые сосредотачиваются на улучшении гигиены вашей безопасности. На панели мониторинга соответствия нормативным требованиям вы можете просмотреть набор стандартов соответствия со всеми требованиями, где поддерживаемые требования сопоставляются с применимыми оценками безопасности. Благодаря этому вы можете просматривать уровень соответствия по отношению к стандарту на основе состояния этих оценок.

Представление панели мониторинга соответствия нормативным требованиям помогает вам сосредоточить внимание на проблемах соответствия требованиям важного для вас стандарта или норматива. Это конкретное представление также позволяет вам непрерывно отслеживать оценку соответствия в динамических облачных и гибридных средах с течением времени.

>[!NOTE]
> По умолчанию Центр безопасности поддерживает следующие нормативные стандарты: Azure CIS, PCI DSS 3.2, ISO 27001 и SOC TSP. 
>
> Функция [динамических пакетов соответствия (предварительная версия)](update-regulatory-compliance-packages.md) позволяет обновить стандарты, отображенные на панели мониторинга соответствия нормативным требованиям, с помощью новых *динамических* пакетов. Вы можете также использовать эту предварительную версию функции для добавления новых пакетов соответствия и мониторинга соответствия требованиям дополнительных стандартов. 

1.  В главном меню Центра безопасности в разделе **Политика и соответствие** выберите **Соответствие нормативным требованиям**. <br>
В верхней части экрана вы увидите панель мониторинга с обзором состояния соответствия с набором поддерживаемых нормативов соответствия. Вы можете увидеть общую оценку соответствия и число удачных и неудачных оценок, связанных с каждым стандартом.

    ![высокая достоверность для описания компьютера](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Выберите вкладку для необходимого стандарта соответствия. Вы увидите список всех элементов управления для этого стандарта. Для применимых элементов управления вы можете просмотреть сведения об удачных и неудачных оценках, связанных с этим элементом управления. Некоторые элементы управления отображаются серым цветом. С этими элементами управления не связаны оценки Центра безопасности. Проанализируйте требования для них и оцените их в своей среде самостоятельно. Некоторые из них могут относиться к процессу и не быть техническими.

    ![Вкладка соответствия](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Чтобы создать и скачать сводный отчет о текущем состоянии соответствия конкретному стандарту в формате PDF, щелкните **Скачать отчет**.

    Этот отчет содержит общую сводку по соответствию требованиям выбранного стандарта согласно данным оценки Центра безопасности. Сведения в отчете упорядочены по элементам управления, предписываемым этим стандартом. Отчет можно предоставить релевантным участникам. Он может послужить свидетельством для внутренних и внешних аудиторов.

    ![загрузить](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Улучшение уровня соответствия

Учитывая сведения на панели мониторинга соответствия нормативным требованиям, вы можете улучшить уровень соответствия, выполнив рекомендации непосредственно на панели мониторинга.

1.  Щелкните любую из неудачных оценок, которые отображаются на панели мониторинга, чтобы просмотреть сведения об этой рекомендации. Каждая рекомендация включает набор действий по устранению, которые следует выполнить для разрешения проблемы.

1.  Вы можете выбрать определенный ресурс для просмотра дополнительных сведений и выполнить рекомендацию для него. <br>Например, на вкладке **Azure CIS standard** (Стандарт Azure CIS) вы можете щелкнуть рекомендацию **Обязательная безопасная передача в учетную запись хранения**.

    ![рекомендация соответствия](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. После того как вы просмотрите сведения о рекомендации и выберите неработоспособный ресурс, вы перейдете непосредственно к интерфейсу включения **безопасного перемещения хранилища** на портале Azure.

    Дополнительные сведения о том, как применить рекомендации, см. в разделе [Внедрение рекомендаций по безопасности в центре безопасности Azure](security-center-recommendations.md).

    ![рекомендация соответствия](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Предприняв действие для выполнения рекомендаций, вы увидите результат в отчете на панели мониторинга соответствия, так как оценка соответствия улучшится.

    > [!NOTE]
    > Оценки выполняются примерно каждые 12 часов, поэтому вы увидите влияние на данные соответствия только после выполнения оценок.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали об использовании панели мониторинга соответствия нормативным требованиям Центра безопасности, чтобы:

-   просматривать и отслеживать ваш уровень соответствия относительно важных для вас стандартов и нормативов;

-   улучшить свое состояние соответствия, выполняя необходимые рекомендации и просматривая улучшение оценки соответствия.

Панель мониторинга соответствия нормативным требованиям может значительно упростить процесс обеспечения соответствия и сократить время, требуемое для сбора свидетельства соответствия для среды Azure и гибридной среды.

Дополнительные сведения:

-   [Обновление пакетов обеспечения совместимости на панели мониторинга соответствия нормативным требованиям (предварительная версия)](update-regulatory-compliance-packages.md). Узнайте об этой предварительной версии функции, которая позволяет обновить стандарты, отображенные на панели мониторинга соответствия нормативным требованиям, с помощью новых *динамических* пакетов. Вы можете также использовать эту предварительную версию функции для добавления новых пакетов соответствия и мониторинга соответствия требованиям дополнительных стандартов. 

-   [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.

-   [Рекомендации по безопасности в Центре безопасности Azure](security-center-recommendations.md). Узнайте о том, как рекомендации Центра безопасности Azure могут помочь вам защитить ресурсы Azure.

-   [Повышение оценки безопасности в Центре безопасности Azure](security-center-secure-score.md). Узнайте том, как определить приоритеты уязвимостей и рекомендаций по безопасности для максимального улучшения уровня безопасности.

-   [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md). Часто задаваемые вопросы об использовании этой службы.

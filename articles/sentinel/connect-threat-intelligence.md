---
title: Подключение данных разведки угроз к Azure Sentinel Документы Майкрософт
description: Узнайте о том, как подключить данные разведки угроз к Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: eec07a01edc6b126bb7cd3a814912ea5c5b14195
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529095"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Подключение данных от поставщиков информации об угрозах

> [!IMPORTANT]
> Разъемы данных Threat Intelligence в Azure Sentinel в настоящее время находятся в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel позволяет импортировать индикаторы угроз, которые использует организация, что может повысить способность аналитиков безопасности обнаруживать и расставлять приоритеты известных угроз. Некоторые функции из Azure Sentinel становятся доступными или расширяются:

- **Аналитика** включает в себя набор запланированных шаблонов правил, которые можно включить для создания оповещений и инцидентов на основе совпадений событий журнала из ваших индикаторов угроз.

- **В учебниках** приведена обобщенные сведения о индикаторах угроз, импортируемых в Azure Sentinel, и любых оповещений, полученных из правил аналитики, которые соответствуют показателям угроз.

- **Охотничьи** запросы позволяют следователям по безопасности использовать индикаторы угроз в контексте общих сценариев охоты.

- **Ноутбуки** могут использовать индикаторы угроз при расследовании аномалий и охоте на вредоносное поведение.

Вы можете передавать индикаторы угроз в Azure Sentinel с помощью одного из продуктов интегрированной платформы анализа угроз (TIP), перечисленных в следующем разделе, подключившись к серверам TAXII или с помощью прямой интеграции с [API Microsoft Graph Security tiIndicators.](https://aka.ms/graphsecuritytiindicators)

## <a name="integrated-threat-intelligence-platform-products"></a>Интегрированные продукты платформы анализа угроз

- [MISP Open Source Threat Intelligence Platform](https://www.misp-project.org/)
    
    Для примера скрипта, который предоставляет клиентам экземпляры MISP для переноса [MISP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)индикаторов угроз в API безопасности Microsoft Graph, см.

- [Аномали ThreatStream](https://www.anomali.com/products/threatstream)

    Чтобы загрузить ThreatStream Integrator and Extensions, а также инструкции по подключению разведки ThreatStream к API безопасности Microsoft Graph, смотрите страницу [загрузок ThreatStream.](https://ui.threatstream.com/downloads)

- [Пало-Альто Сети MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Для получения инструкций [см. Отправка МОК на API безопасности Microsoft Graph с помощью MineMeld.](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)

- [Платформа ThreatConnect](https://threatconnect.com/solution/)

    Для получения информации смотрите [интеграцию ThreatConnect](https://threatconnect.com/integrations/) и ищите API безопасности Microsoft Graph на странице.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Подключите Azure Sentinel к вашей платформе разведки угроз

## <a name="prerequisites"></a>Предварительные требования  

- Роль администратора Azure AD глобального администратора или администратора безопасности в предоставлении разрешений вашему продукту TIP или пользовательскому приложению, использующему прямую интеграцию с API Microsoft Graph Security tiIndicators.

- Прочитайте и напишите разрешения в рабочее пространство Azure Sentinel для хранения индикаторов угроз.

## <a name="instructions"></a>Instructions

1. [Зарегистрируйте приложение](/graph/auth-v2-service#1-register-your-app) в каталоге Azure Active, чтобы получить идентификатор приложения, секрет приложения и идентификатор арендатора Azure Active Directory. Эти значения необходимы при настройке интегрированного продукта TIP или приложения, использующемпрямую прямую интеграцию с API Microsoft Graph Security.

2. [Настройте разрешения API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) для зарегистрированного приложения: Добавьте в зарегистрированное приложение разрешение приложения Microsoft Graph **ThreatIndicators.ReadWrite.OwnedBy.**

3. Попросите администратора-арендатора Azure Active Directory дать согласие администратора на зарегистрированное заявление для вашей организации. С портала Azure: Приложение для**регистрации** > **\<_app name_>** приложений **Azure Active Directory** > App имя > **View API Разрешения** > на**получение разрешения на \< _имя_>клиента.**

4. Нанастройка продукта или приложения TIP, использующее прямую интеграцию с API Microsoft Graph Security tiIndicators, для отправки индикаторов в Azure Sentinel, указав следующее:
    
    а. Значения идентификатора, секретного и идентификатора зарегистрированного приложения.
    
    b. Для целевого продукта укажите Azure Sentinel.
    
    c. Для действия укажите оповещение.

5. На портале Azure перейдите на**разъемы данных** **Azure Sentinel** > Data, а затем выберите разъем **платформ threat Intelligence Platforms (Preview).**

6. Выберите **открытую страницу разъема,** а затем **подключитесь.**

7. Для просмотра индикаторов угроз, импортируемых в Azure Sentinel, перейдите на **Azure Sentinel - Logs** > **SecurityInsights,** а затем расширьте **ThreatIntelligenceIndicator.**

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Подключение Azure Sentinel к серверам TAXII

## <a name="prerequisites"></a>Предварительные требования  

- Прочитайте и напишите разрешения в рабочее пространство Azure Sentinel для хранения индикаторов угроз.

- TAXII 2.0 сервер URI и идентификатор коллекции.

## <a name="instructions"></a>Instructions

1. На портале Azure перейдите на**разъемы данных** **Azure Sentinel** > Data, а затем выберите разъем **Threat Intelligence - TAXII (Preview).**

2. Выберите **страницу Open connector**.

3. Укажите требуемую и необязательную информацию в текстовых коробках.

4. Выберите **Добавить,** чтобы включить подключение к серверу TAXII 2.0.

5. Если у вас есть дополнительные серверы TAXII 2.0: Повторите шаги 3 и 4.

6. Для просмотра индикаторов угроз, импортируемых в Azure Sentinel, перейдите на **Azure Sentinel - Logs** > **SecurityInsights,** а затем расширьте **ThreatIntelligenceIndicator.**

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить поставщика данных о разведке угроз к Azure Sentinel. Чтобы узнать больше о Azure Sentinel, смотрите следующие статьи.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

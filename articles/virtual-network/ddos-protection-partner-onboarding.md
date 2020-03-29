---
title: Партнерство со стандартом защиты DDoS Azure
description: Понимание возможностей партнерства, включенных стандартом защиты DDoS Azure.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849686"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Партнерство со стандартом защиты DDoS Azure
В этой статье описаны возможности партнерства, включенные стандартом DDoS Azure DDoS. Эта статья призвана помочь менеджерам по продуктам и роли развития бизнеса понять пути инвестирования и дать представление о партнерских ценностных предложениях.

## <a name="background"></a>Историческая справка
Распределенные атаки типа «отказ в обслуживании» (DDoS) являются одной из главных проблем доступности и безопасности, озвученных клиентами, перемещающих свои приложения в облако. С вымогательством и hacktivism является общей мотивацией За DDoS-атаки, они постоянно растет в типе, масштабе и частоте возникновения, поскольку они относительно легко и дешево для запуска.

Azure DDoS Protection обеспечивает контрмеры против самых сложных DDoS-угроз, используя глобальный масштаб сетей Azure. Сервис предоставляет расширенные возможности dDoS-смягчения для приложений и ресурсов, развернутых в виртуальных сетях.

Технологические партнеры могут защитить ресурсы своих клиентов с помощью Azure DDoS Protection Standard для решения проблем доступности и надежности из-за DDoS-атак.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Введение в стандарт защиты DDoS Azure
Стандарт защиты DDoS Azure предоставляет расширенные возможности dDoS-смягчения от DDoS-атак. Ниже приведены основные особенности службы DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Адаптивная настройка в режиме реального времени
Для каждого защищенного приложения Стандарт защиты DDoS Azure автоматически настраивает пороги политики dDoS по политике смягчения на основе шаблонов профиля трафика приложения. Служба выполняет эту настройку с помощью двух аналитических инструментов:

- Автоматическое изучение шаблонов трафика уровня 3 и 4 для каждого клиента (протокола IP).
- Минимизация ложных срабатываний, учитывая, что масштаб Azure позволяет поглощать значительный объем трафика.

![Адаптивная настройка в реальном времени](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Аналитика атак, телеметрия, мониторинг и оповещение
Защита от DDoS Azure выявляет и смягчает DDoS-атаки без какого-либо вмешательства пользователя.

- Если защищенный ресурс находится в подписке, охватываемой Центром безопасности Azure, DDoS Protection Standard автоматически отправляет оповещение в Центр безопасности всякий раз, когда DDoS-атака обнаружена и смягчена против защищенного приложения.
- Кроме того, чтобы получить уведомление, когда есть активное смягчение для защищенного общественного IP, вы можете [настроить оповещение](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) на метрику под DDoS-атакой или нет.
- Кроме того, можно создать оповещения для других DDoS-метрик и [настроить аналитику атак,](manage-ddos-protection.md#configure-ddos-attack-analytics) чтобы понять масштаб атаки, трафик, отбрасываемый, векторы атак, лучшие участники и другие детали.

![DDoS метрики](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS быстрого реагирования (DRR)
Клиенты DDoS Protection Standard имеют доступ к [группе быстрого реагирования](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) во время активной атаки. DRR может помочь в расследовании атак, пользовательских смягчениях во время атаки и после атаки анализа.

### <a name="sla-guarantee-and-cost-protection"></a>Защита гарантий и затрат SLA
Стандартная служба DDoS Protection Standard покрывается 99,99% SLA, а защита затрат обеспечивает кредиты на ресурсы для масштабирования во время документально подтвержденной атаки. Для получения дополнительной информации [см.](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)

## <a name="featured-partner-scenarios"></a>Рекомендуемые сценарии партнеров
Ниже приведены основные преимущества, которые можно получить, интегрируясь со стандартом Защиты DDoS Azure:

- Предлагаемые партнерами услуги (балансер нагрузки, брандмауэр веб-приложений, брандмауэр и т.д.) для своих клиентов автоматически защищены (белая маркировка) стандартом защиты DDoS Azure в задней части.
- Партнеры имеют доступ к аналитике атак Azure DDoS Standard ИС-аналитике и телеметрии, которую они могут интегрировать со своими собственными продуктами, предлагая единый клиентский опыт.  
- Партнеры имеют доступ к поддержке быстрого реагирования DDoS даже при отсутствии быстрого реагирования Azure на проблемы, связанные с DDoS.
- Защищенные приложения партнеров поддерживаются гарантией DDoS SLA и защитой затрат в случае DDoS-атак.

## <a name="technical-integration-overview"></a>Обзор технической интеграции
Возможности партнерского партнерства Azure DDoS Standard доступны через портал Azure, AI и CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Интеграция со стандартом защиты DDoS
Для настройки интеграции с Azure DDoS-стандартом для защиты dDoS требуется следующие шаги:
1. Создайте план Защиты ОтDS в желаемой (партнерской) подписке. Для пошаговых инструкций [см.](manage-ddos-protection.md#create-a-ddos-protection-plan)
   > [!NOTE]
   > Для данного арендатора необходимо создать только 1 план защиты DDoS. 
2. Развертывание службы с общедоступной конечной точкой в подписках (партнера), таких как баланселизатор нагрузки, брандмауэры и брандмауэр веб-приложений. 
3. Включите стандарт DDoS-защиты Azure в виртуальной сети службы с общедоступными конечными точками с использованием плана DDoS Protection Plan, созданного на первом этапе. Для инструкций stpe-by-step [см.](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > После включения Стандарта Защиты ОтDS В виртуальной сети все общедоступные ИС в этой виртуальной сети автоматически защищены. Происхождение этих общедоступных ИП может быть как в Azure (клиентская подписка), либо за пределами Azure. 
4. Дополнительно интегрируйте телеметрию Azure DDoS Protection Standard и аналитику атак в панель мониторинга для конкретных клиентов. Для получения дополнительной информации об использовании телеметрии [см.](manage-ddos-protection.md#use-ddos-protection-telemetry) Для получения дополнительной информации о [Configure DDoS attack analytics](manage-ddos-protection.md#configure-ddos-attack-analytics) настройке аналитики атак см.

### <a name="onboarding-guides-and-technical-documentation"></a>Гиды по борту и техническая документация

- [Страница службы "Защита от атак DDoS Azure"](https://azure.microsoft.com/services/ddos-protection/)
- [Документация по службе "Защита от атак DDoS Azure"](ddos-protection-overview.md)
- [Ссылка API Защиты DDoS Azure](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Ссылка на API виртуальной сети Azure](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Получить справку

- Если у вас возникли вопросы об интеграции приложений, услуг или продуктов в стандарте защиты DDoS Azure, осваивайтесь [сообществом безопасности Azure.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Следите за обсуждениями по [переполнению стеков](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Выйти на рынок

- Основной программой для партнерства с корпорацией Майкрософт является [партнерская сеть Майкрософт.](https://partner.microsoft.com/) - Интеграция Microsoft Graph Security попадает в трек [MPN Independent Software Vendor (ISV).](https://partner.microsoft.com/saas-solution-guide)
- [Ассоциация интеллектуальной безопасности Майкрософт](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) — это программа специально для партнеров Майкрософт по безопасности, которая поможет обогатить ваши продукты безопасности и улучшить возможности ваших интеграций с продуктами Microsoft Security.

## <a name="next-steps"></a>Дальнейшие действия
Просмотр существующих интеграций партнеров:

- [Барракуда ВАФ-а-сервис](https://www.barracuda.com/waf-as-a-service)
- [Облако Azure WAF от Radware](https://www.radware.com/resources/microsoft-azure/)

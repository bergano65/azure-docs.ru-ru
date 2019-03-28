---
title: Служба приложений Azure для поддержки среды выполнения Java Windows
description: Этот раздел содержит инструкции среда выполнения Java поддержки для службы приложений Azure на Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523045"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Оператор среды выполнения Java поддержки для службы приложений в Windows

## <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Основной номер версии обновления будут предоставляться через новые параметры среды выполнения в службе приложений Azure для Windows. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

## <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

## <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Поддержка разработки

Поддержка [с поддержкой Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) доступна через Microsoft при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) с [указанием план поддержки Azure](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Дальнейшие действия

Этот раздел содержит инструкции среда выполнения Java поддержки для службы приложений Azure на Windows.
- Дополнительные сведения о размещении веб-приложений в службе приложений Azure см. в разделе [Обзор службы приложений](overview.md).
- Сведения о Java на Azure разработки см. в разделе [Azure для Java центра разработки](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

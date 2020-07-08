---
title: Часто задаваемые вопросы о FHIR Services в Azure — API Azure для FHIR
description: Получите ответы на часто задаваемые вопросы о API Azure для FHIR, например о местоположении хранения данных для интерфейсов API FHIR и поддержки версий.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870984"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Часто задаваемые вопросы об API Azure для FHIR

## <a name="what-is-fhir"></a>Что такое FHIR?
Ресурсы по быстрому взаимодействию в сфере здравоохранения (FHIR — «пожар») — это стандарт взаимодействия, предназначенный для обмена данными здравоохранения между различными системами исправности. Этот стандарт был разработан HL7 Организацией и принят организациями здравоохранения по всему миру. Самой последней версией FHIR является R4 (выпуск 4). API Azure для FHIR поддерживает R4, а также предыдущую версию STU3 (Standard для пробного использования 3). Дополнительные сведения о FHIR см. в [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Данные за API FHIR хранятся в Azure?

Да, данные хранятся в управляемых базах данных в Azure. API Azure для FHIR не предоставляет прямой доступ к базовому хранилищу данных.

## <a name="what-identity-provider-do-you-support"></a>Какой поставщик удостоверений поддерживается?

В настоящее время в качестве поставщика удостоверений поддерживается Microsoft Azure Active Directory.

## <a name="what-fhir-version-do-you-support"></a>Какая версия FHIR поддерживается?

Мы поддерживаем версии 4.0.0 и 3.0.1 как в API Azure для FHIR (PaaS), так и на сервере FHIR для Azure (с открытым кодом).

Дополнительные сведения см. в разделе [Поддерживаемые функции](fhir-features-supported.md). Узнайте о том, что изменилось между версиями в [журнале версий для HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>В чем разница между сервером Microsoft FHIR с открытым исходным кодом для Azure и API Azure для FHIR?

API Azure для FHIR — это размещенная и управляемая версия сервера Microsoft FHIR с открытым исходным кодом для Azure. В управляемой службе Корпорация Майкрософт предоставляет все услуги по обслуживанию и обновлению. 

Если вы используете FHIR Server для Azure, у вас есть прямой доступ к базовым службам. Но вы также отвечаете за обслуживание и обновление сервера и все необходимые условия соответствия, если вы храните данные фи.

С точки зрения разработки все компоненты сначала развертываются на сервере Microsoft FHIR с открытым исходным кодом для Azure. После проверки в открытом источнике оно будет выпущено для решения PaaS Azure API for FHIR. Время между выпуском в с открытым исходным кодом и PaaS зависит от сложности функции и других приоритетов плана. 

## <a name="what-is-smart-on-fhir"></a>Что такое SMART on FHIR?

Интеллектуальные (подставляемые медицинские приложения и многократно используемые технологии) на FHIR — это набор открытых спецификаций для интеграции партнерских приложений с серверами FHIR и другими системными ИТ-системами, такими как электронные записи о работоспособности и обмен информацией о работоспособности. Создав ИНТЕЛЛЕКТУАЛЬНое приложение на FHIR, вы сможете обеспечить доступ к приложению и использовать его в различных системах.
Проверка подлинности и API Azure для FHIR. Чтобы получить дополнительные сведения о смарт-разработе, перейдите в [интеллектуальную Подправку](https://smarthealthit.org/).

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы прочитали некоторые из часто задаваемых вопросов об API Azure для FHIR. Дополнительные сведения о поддерживаемых функциях FHIR Server для Azure:
 
>[!div class="nextstepaction"]
>[Поддерживаемые функции FHIR](fhir-features-supported.md)
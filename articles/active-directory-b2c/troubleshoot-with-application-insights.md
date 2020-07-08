---
title: Устранение неполадок пользовательских политик с помощью Application Insights
titleSuffix: Azure AD B2C
description: Настройка Application Insights для трассировки выполнения пользовательских политик.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fad29c32731ee2470354a51acf32e350eb0c4cfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384878"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Собирайте журналы Azure Active Directory B2C с помощью Application Insights

В этой статье приводятся действия по сбору журналов из Active Directory B2C (Azure AD B2C), чтобы можно было диагностировать проблемы с пользовательскими политиками. Application Insights позволяет диагностировать исключения и визуализировать проблемы производительности в приложениях. Azure AD B2C включает функцию отправки данных в Application Insights.

Подробные журналы действий, описанные здесь, должны быть включены **только** во время разработки пользовательских политик.

> [!WARNING]
> Не включайте режим разработки в рабочей среде. Журналы собираются все утверждения, отправленные поставщикам удостоверений и от них. Разработчик несет ответственность за любые персональные данные, собранные в журналах Application Insights. Эти подробные журналы собираются только в том случае, если политика помещается в **режим разработчика**.

## <a name="set-up-application-insights"></a>Настройка Application Insights

Создайте экземпляр Application Insights в подписке, если он еще не создан.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий подписку Azure (а не Azure AD B2C каталог).
1. Выберите **создать ресурс** в меню навигации слева.
1. Найдите и выберите **Application Insights**, а затем щелкните **создать**.
1. Заполните форму, выберите **проверить и создать**, а затем щелкните **создать**.
1. После завершения развертывания выберите **Переход к ресурсу**.
1. В меню **Настройка** в Application Insights выберите пункт **свойства**.
1. Запишите **ключ инструментирования** для использования на следующем шаге.

## <a name="configure-the-custom-policy"></a>Настройка настраиваемой политики

1. Откройте файл проверяющей стороны (RP), например *SignUpOrSignin.xml*.
1. Добавьте следующие атрибуты в элемент `<TrustFrameworkPolicy>`.

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Добавьте `<UserJourneyBehaviors>` в узел дочерний узел, если он еще не создан `<RelyingParty>` . Он должен располагаться сразу после `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`. Обязательно замените `{Your Application Insights Key}` Application Insightsным **ключом инструментирования** , записанным ранее.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`сообщает ApplicationInsights, что необходимо ускорить передачу данных телеметрии через конвейер обработки. Подходит для разработки, но ограничена большими объемами томов.
    * `ClientEnabled="true"`отправляет скрипт на стороне клиента ApplicationInsights для отслеживания просмотра страниц и ошибок на стороне клиента. Их можно просмотреть в таблице **бровсертимингс** на портале Application Insights. При установке `ClientEnabled= "true"` вы добавляете в скрипт страницы Application Insights и получаете время загрузки страниц и вызовов AJAX, количество, сведения об исключениях браузера и СБОЯХ AJAX, а также число пользователей и сеансов. Это поле является **необязательным**и по `false` умолчанию имеет значение.
    * Параметр `ServerEnabled="true"` отправляет существующие данные JSON UserJourneyRecorder как пользовательское событие в Application Insights.

    Пример:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Отправьте политику.

## <a name="see-the-logs-in-application-insights"></a>Просмотр журналов в Application Insights

Для просмотра новых журналов в Application Insights требуется небольшая задержка (обычно менее пяти минут).

1. Откройте созданный ресурс Application Insights на [портале Azure](https://portal.azure.com).
1. В меню **Обзор** выберите пункт **аналитика**.
1. Откройте новую вкладку в Application Insights

Ниже приведен список запросов, которые можно использовать для просмотра журналов.

| Запрос | Описание |
|---------------------|--------------------|
`traces` | Просмотр всех журналов, созданных Azure AD B2C |
`traces | where timestamp > ago(1d)` | Просмотр всех журналов, созданных Azure AD B2C за последний день

Записи могут быть длинными. Выполните экспорт в CSV-файл, чтобы изучить их подробнее.

Дополнительные сведения о запросах см. [в разделе Обзор запросов журналов в Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Дальнейшие шаги

Для разработчиков удостоверений сообщество разработало средство просмотра пути взаимодействия пользователя. Оно считывается из экземпляра Application Insights и обеспечивает хорошо структурированное представление событий пути взаимодействия пользователя. Исходный код можно получить и развернуть в собственном решении.

Пользовательский проигрыватель не поддерживается корпорацией Майкрософт и становится доступным строго как есть.

Версию средства просмотра, считывающего события из Application Insights на GitHub, можно найти здесь:

[Azure-Samples/Active-Directory-B2C-Advanced-Policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

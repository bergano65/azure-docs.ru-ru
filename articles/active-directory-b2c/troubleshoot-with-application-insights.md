---
title: Устранение пользовательских политик с помощью приложений Insights
titleSuffix: Azure AD B2C
description: Как настроить Application Insights для отслеживания выполнения пользовательских политик.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186273"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Сбор журналов Active Directory B2C с помощью анализов приложений

В этой статье приведены шаги для сбора журналов из Active Directory B2C (Azure AD B2C), чтобы можно было диагностировать проблемы с пользовательскими политиками. Application Insights позволяет диагностировать исключения и визуализировать проблемы производительности в приложениях. Azure AD B2C включает функцию отправки данных в Application Insights.

Подробные журналы активности, описанные здесь, должны быть включены **только** во время разработки пользовательских политик.

> [!WARNING]
> Не включайте режим разработки в производстве. Логи собирают все претензии, отправленные и от поставщиков идентификационных данных. Вы, как разработчик, берете на себя ответственность за любые персональные данные, собранные в журналах Application Insights. Эти подробные журналы собираются только тогда, когда политика помещена в **DEVELOPER MODE.**

## <a name="set-up-application-insights"></a>Настройка анализов приложений

Если у вас его еще нет, создайте экземпляр Application Insights в подписке.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **подписки каталога** в верхнем меню, а затем выберите каталог, содержащий подписку Azure (не каталог Azure AD B2C).
1. Выберите **Создать ресурс** в меню левой навигации.
1. Поиск и выберите **Application Insights,** затем выберите **Создать**.
1. Заполните форму, выберите **Обзор и создайте,** а затем выберите **Создать**.
1. После завершения развертывания выберите **«Перейти к ресурсу».**
1. Под **настроить** в меню Application Insights выберите **Свойства**.
1. Запишите **INSTRUMENTATION KEY** для использования на более позднем этапе.

## <a name="configure-the-custom-policy"></a>Настройка пользовательской политики

1. Откройте файл relying party (RP), например *SignUpOrSignin.xml*.
1. Добавьте следующие атрибуты в элемент `<TrustFrameworkPolicy>`.

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Если он еще не существует, `<UserJourneyBehaviors>` добавьте в `<RelyingParty>` узла узла узла узла детский узла. Он должен быть `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`расположен сразу после .
1. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`. Убедитесь в `{Your Application Insights Key}` том, чтобы заменить приложение Исследования **инструменты ключ,** который вы записали ранее.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`сообщает ApplicationInsights ускорить телеметрию через конвейер обработки. Хорошо для разработки, но ограничено большими объемами.
    * `ClientEnabled="true"`отправляет сценарий клиента ApplicationInsights для отслеживания просмотра страниц ы и ошибок на стороне клиента. Вы можете просмотреть их в таблице **browserTimings** на портале Application Insights. При `ClientEnabled= "true"`настройке вы добавляете Application Insights в сценарий страницы, и вы получаете тайминги нагрузок страниц ы и ajax звонки, подсчеты, детали исключений браузера и сбоев AJAX, а также количество пользователей и сеансов. Это поле является **необязательным,** и устанавливается `false` по умолчанию.
    * Параметр `ServerEnabled="true"` отправляет существующие данные JSON UserJourneyRecorder как пользовательское событие в Application Insights.

    Пример:

    ```XML
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

Существует небольшая задержка, как правило, менее пяти минут, прежде чем вы сможете увидеть новые журналы в Application Insights.

1. Откройте созданный ресурс Application Insights на [портале Azure](https://portal.azure.com).
1. В меню **«Обзор»** выберите **Аналитику**.
1. Откройте новую вкладку в Application Insights

Вот список запросов, которые можно использовать для просмотра журналов:

| Запрос | Описание |
|---------------------|--------------------|
`traces` | Просмотр всех журналов, созданных Azure AD B2C |
`traces | where timestamp > ago(1d)` | Просмотр всех журналов, созданных Azure AD B2C за последний день

Записи могут быть длинными. Выполните экспорт в CSV-файл, чтобы изучить их подробнее.

Для получения дополнительной информации о запросе смотрите [обзор запросов журналов в Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

Для разработчиков удостоверений сообщество разработало средство просмотра пути взаимодействия пользователя. Оно считывается из экземпляра Application Insights и обеспечивает хорошо структурированное представление событий пути взаимодействия пользователя. Исходный код можно получить и развернуть в собственном решении.

Пользователь путешествия плеер не поддерживается корпорацией Майкрософт, и предоставляется строго как есть.

Вы можете найти версию зрителя, который читает события из приложения Исследования на GitHub, здесь:

[Azure-Образцы/активный каталог-b2c-продвинутые политики](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

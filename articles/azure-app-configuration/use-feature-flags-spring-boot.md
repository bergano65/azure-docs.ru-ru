---
title: Краткое руководство по использованию флагов функций в приложении Spring Boot — Конфигурация приложений | Документация Майкрософт
description: Из этого руководства вы узнаете, как реализовать флаги функций в приложениях Spring Boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687204"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Руководство по Использование флагов компонентов в приложении Spring Boot

Библиотеки управления функциями Spring Boot обеспечивают поддержку реализации флагов функций в приложении Spring Boot. Эти библиотеки позволяют декларативно добавлять флаги функций в код.

Библиотеки управления функциями также управляют жизненным циклом флагов функций в фоновом режиме. Например, библиотеки отвечают за обновление и кэширование состояний флагов или гарантируют неизменность состояния флага во время вызова запроса. Кроме того, библиотека Spring Boot предоставляет интеграции, включая действия контроллера MVC, маршруты и ПО промежуточного слоя.

В [кратком руководстве по добавлению флагов функций в приложение Spring Boot](./quickstart-feature-flag-spring-boot.md) описано несколько использующихся при этом методов. В нашем руководстве такие методы рассматриваются более подробно.

Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> * Добавить флаги функций в ключевых частях приложения для управления доступностью функций.
> * Как выполнить интеграцию с Конфигурацией приложений при использовании этой службы для управления флагами функций.

## <a name="set-up-feature-management"></a>Настройка управления функциями

Диспетчер функций Spring Boot `FeatureManager` возвращает флаги функций из собственной системы конфигурации платформы. Таким образом, вы можете определить флаги функций приложения, используя любой источник конфигурации, поддерживаемый Spring Boot, включая локальный файл *bootstrap.yml* или переменные среды. `FeatureManager` полагается на внедрение зависимостей. Вы можете зарегистрировать службы управления функциями с помощью стандартных соглашений:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Рекомендуем хранить флаги функций вне приложения и управлять ими отдельно. Так вы можете изменять состояния флагов в любое время и немедленно применять эти изменения в приложении. Конфигурация приложений — это централизованное расположение для организации всех флагов функций и управления такими флагами с помощью выделенного пользовательского интерфейса портала. Конфигурация приложений также предоставляет приложению флаги непосредственно через клиентские библиотеки Spring Boot.

Самый простой способ подключить приложение Spring Boot к Конфигурации приложений — использовать поставщика конфигураций.

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Объявление флага функции

Каждый флаг функции состоит из двух частей: имени и списка из одного или нескольких фильтров, использующихся, чтобы оценить, *включена* ли функция (то есть, если его значение равно `True`). Фильтр определяет вариант использования, при котором должна быть включена функция.

Если флаг функции имеет несколько фильтров, список фильтров проходится по порядку, пока один из фильтров не определит, что функция должна быть включена. На этом этапе флаг функции *включен*, и все остальные результаты фильтрации пропускаются. Если фильтр не указывает, что эта функция должна быть включена, флаг функции остается *отключенным*.

Диспетчер функций поддерживает *application.yml* в качестве источника конфигурации для флагов функций. В следующем примере показано, как настроить флаги функций в YAML-файле:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

В соответствии с соглашением раздел `feature-management` этого YAML-документа используется для параметров флагов функций. Предыдущий пример показывает три флага функций с фильтрами, определенными в свойстве `EnabledFor`:

* Флаг `FeatureA` *включен*.
* Флаг `FeatureB` *отключен*.
* `FeatureC` указывает фильтр с именем `Percentage` и свойством `Parameters`. `Percentage` — это настраиваемый фильтр. В этом примере `Percentage` задает 50-процентную вероятность того, что флаг `FeatureC` может быть *включен*.

## <a name="feature-flag-checks"></a>Проверка флага функции

В базовой схеме управления функциями сначала проверяется, *включен* ли флаг функции. Если это так, диспетчер функций выполняет действия, содержащиеся в функции. Например:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Внедрение зависимостей

В Spring Boot доступ к диспетчеру функций `FeatureManager` можно получить с помощью внедрения зависимостей:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Действия контроллера

В контроллерах MVC с помощью атрибута `@FeatureGate` можно управлять объектом включения: определенное действие. Для выполнения указанного ниже действия `Index` требуется, чтобы флаг `FeatureA` был *включен*.

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Когда контроллер MVC или действие блокируется из-за того, что флаг функции контроллера *отключен*, вызывается зарегистрированный интерфейс `IDisabledFeaturesHandler`. Интерфейс `IDisabledFeaturesHandler` по умолчанию возвращает клиенту код состояния 404 без текста ответа.

## <a name="mvc-filters"></a>Фильтры MVC

Фильтры MVC можно настроить таким образом, чтобы они активировались в зависимости от состояния флага функции. С помощью приведенного ниже кода добавляется фильтр MVC с именем `FeatureFlagFilter`. Этот фильтр активируется в пределах конвейера MVC, только если флаг `FeatureA` включен.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Маршруты

Флаги функции можно использовать для перенаправления маршрутов. Следующий код перенаправит пользователя из `FeatureA`.

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как с помощью библиотек `spring-cloud-azure-feature-management-web` реализовать флаги функций в приложении Spring Boot. Подробные сведения о поддержке управления функциями в службе "Конфигурация приложений" и Spring Boot см. по следующим ссылкам:

* [Пример кода для флага функции Spring Boot](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Управление флагами компонентов](./manage-feature-flags.md)

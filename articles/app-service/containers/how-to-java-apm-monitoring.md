---
title: Настройка Java APM и инструментов мониторинга с помощью Службы приложений Azure в Linux
description: Сведения об отправке журналов и метрик поставщикам APM NewRelic и AppDynamics для приложений Java, работающих под управлением Linux в службе приложений
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577173"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Практическое руководство. Инструменты мониторинга производительности приложений Java в Службе приложений Azure под управлением Linux

В этой статье показано, как подключить приложения Java, развернутые в Службе приложений Azure в Linux, к платформам мониторинга производительности (APM) приложений NewRelic и AppDynamics.

## <a name="configure-new-relic"></a>Настройка New Relic
1. Создайте учетную запись NewRelic на сайте [NewRelic.com](https://newrelic.com/signup)
1. Скачайте агент Java с NewRelic. Его имя файла будет аналогично `newrelic-java-x.x.x.zip`.
1. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
1. [Подключитесь к экземпляру службы приложений по протоколу SSH](/azure/app-service/containers/app-service-linux-ssh-support) и создайте каталог `/home/site/wwwroot/apm`. 
1. Отправьте распакованные файлы агента Java NewRelic в каталог `/home/site/wwwroot/apm`. Файлы для агента должны быть в каталоге `/home/site/wwwroot/apm/newrelic`.
1. Измените файл YAML в каталоге `/home/site/wwwroot/apm/newrelic/newrelic.yml` и замените значение лицензии в заполнителе собственным ключом лицензии.
1. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `javaagent` в конец текущего значения.

## <a name="configure-appdynamics"></a>Настройка AppDynamics
1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Скачайте агент Java с веб-сайта AppDynamics. Имя файла будет аналогично `AppServerAgent-x.x.x.xxxxx.zip`.
1. [Подключитесь к экземпляру службы приложений по протоколу SSH](/azure/app-service/containers/app-service-linux-ssh-support) и создайте каталог `/home/site/wwwroot/apm`. 
1. Отправьте файлы агента Java в каталог `/home/site/wwwroot/apm`. Файлы для агента должны быть в каталоге `/home/site/wwwroot/apm/appdynamics`.
1. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>`, где `<YOUR_SITE_NAME>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>`, где `<YOUR_SITE_NAME>` — имя службы приложений.

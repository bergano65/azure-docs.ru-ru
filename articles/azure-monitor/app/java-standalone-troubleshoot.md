---
title: Устранение неполадок Azure Monitor Application Insights для Java
description: Узнайте, как устранять неполадки агента Java для Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 54bf2440dff20fc757f37e3f31a53c57ebd59120
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133197"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Руководство по устранению неполадок: Azure Monitor Application Insights для Java

В этой статье рассматриваются некоторые распространенные проблемы, которые могут возникнуть при инструментировании приложения Java с помощью агента Java для Application Insights. Мы также рассмотрим действия по устранению этих проблем. Application Insights является компонентом службы платформы Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Проверка файла журнала самодиагностики

По умолчанию агент Java 3,0 для Application Insights создает файл журнала с именем `applicationinsights.log` в том же каталоге, в котором находится `applicationinsights-agent-3.0.0.jar` файл.

Этот файл журнала является первым местом для поиска подсказок о любых проблемах, которые могут возникнуть.

## <a name="jvm-fails-to-start"></a>Не удается запустить ВИРТУАЛЬНОЙ машины Java

Если ВИРТУАЛЬНОЙ машины Java не удается запустить с ошибкой "Ошибка открытия ZIP-файла или манифеста JAR", попробуйте повторно Скачать JAR-файл агента, так как он мог быть поврежден при переносе файлов.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Обновление с помощью пакета SDK для Java 2. x Application Insights

Если вы уже используете пакет SDK для Java 2. x Application Insights в приложении, вы можете использовать его. Агент Java 3,0 определит его. Дополнительные сведения см. [в статье обновление с помощью пакета SDK для Java 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Обновление с предварительной версии Application Insights Java 3,0

Если вы выполняете обновление с помощью предварительной версии агента Java 3,0, внимательно просмотрите все [Параметры конфигурации](./java-standalone-config.md) . Структура JSON полностью изменена в выпуске 3,0 общей доступности (общедоступной версии).

Это следующие изменения:

-  Имя файла конфигурации изменено с `ApplicationInsights.json` на `applicationinsights.json` .
-  `instrumentationSettings`Узел больше не существует. Все содержимое в `instrumentationSettings` перемещается на корневой уровень. 
-  Узлы конфигурации, такие как `sampling` ,, `jmxMetrics` и, `instrumentation` `heartbeat` перемещаются из `preview` в корневой уровень.

## <a name="some-logging-is-not-auto-collected"></a>Некоторые журналы не собираются

Ведение журнала захватывается только в том случае, если оно сначала соответствует заданному пороговому значению платформ ведения журналов, а вторая также соответствует пороговому значению Application Insights.

Лучший способ узнать, соответствует ли определенная инструкция ведения журнала заданному пороговому значению платформ ведения журналов, — убедиться, что она отображается в нормальном журнале приложений (например, файл или консоль).

Дополнительные сведения см. в разделе [Конфигурация автоматического сбора журналов](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Импорт SSL-сертификатов

Если вы используете хранилище ключей Java по умолчанию, он уже будет иметь все корневые сертификаты ЦС. Вам не нужно импортировать больше SSL-сертификатов.

Если вы используете настраиваемый хранилище ключей Java, может потребоваться импортировать в него сертификаты SSL Application Insights конечной точки.

### <a name="key-terminology"></a>Ключевая терминология
*Хранилище ключей* — это репозиторий сертификатов, открытых ключей и закрытых ключей. Обычно дистрибутивы пакета разработки Java имеют исполняемый файл для управления ими: `keytool` .

Ниже приведен пример простой команды для импорта SSL-сертификата в хранилище ключей.

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Действия по скачиванию и добавлению SSL-сертификата

1.  Откройте свой любимый браузер и перейдите по `IngestionEndpoint` URL-адресу, указанному в строке подключения, используемой для инструментирования приложения.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Снимок экрана, на котором показана строка подключения Application Insights.":::

2.  Выберите значок **Просмотр сведений о сайте** (блокировка) в браузере, а затем выберите параметр **сертификат** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Снимок экрана с параметром сертификата в сведениях о сайте.":::

3.  Перейдите на вкладку **сведения** и выберите **Копировать в файл**.
4.  Нажмите кнопку **Далее** , выберите **base-64 Encoded X. 509 (. CER)** , а затем снова нажмите кнопку **Далее** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Снимок экрана мастера экспорта сертификатов с выбранным форматом.":::

5.  Укажите файл, в котором вы хотите сохранить SSL-сертификат. Затем нажмите кнопку **Далее**  >  **Готово**. Вы увидите сообщение "Экспорт выполнен успешно".
6.  После создания сертификата необходимо импортировать сертификат в хранилище ключей Java. Используйте [предыдущую команду](#key-terminology) для импорта сертификатов.

> [!WARNING]
> Чтобы получить новый сертификат до истечения срока действия текущего сертификата, необходимо повторить эти действия. Сведения об истечении срока действия можно найти на вкладке **сведения** в диалоговом окне **сертификат** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Снимок экрана, показывающий сведения о SSL-сертификате.":::

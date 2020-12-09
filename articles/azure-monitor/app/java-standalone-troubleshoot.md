---
title: Устранение неполадок — Azure Monitor Application Insights Java
description: Устранение неполадок Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855659"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Устранение неполадок Azure Monitor Application Insights Java

В этой статье мы рассмотрели некоторые распространенные проблемы, с которыми пользователь может столкнуться при инструментировании приложения Java с помощью агента Java, а также действия по устранению этих проблем.

## <a name="self-diagnostic-log-file"></a>Файл журнала самодиагностики

По умолчанию Application Insights Java 3,0 создает файл журнала с именем `applicationinsights.log` в том же каталоге, где `applicationinsights-agent-3.0.0.jar` находится файл.

Этот файл журнала является первым местом для поиска подсказок о любых проблемах, которые могут возникнуть.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Обновление с Application Insights пакета SDK для Java 2. x

См. статью [обновление с пакета SDK для 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Обновление с предварительной версии 3,0

При обновлении с предварительной версии 3,0 внимательно просмотрите все [Параметры конфигурации](./java-standalone-config.md) , так как структура JSON полностью изменилась в выпуске 3,0 в общедоступной версии.

Это следующие изменения:

1.  Имя файла конфигурации изменилось с `ApplicationInsights.json` на `applicationinsights.json` .
2.  `instrumentationSettings`Узел больше не существует. Все содержимое в `instrumentationSettings` перемещается на корневой уровень. 
3.  Узлы конфигурации, такие как `sampling` , `jmxMetrics` и, `instrumentation` `heartbeat` перемещаются из `preview` в корневой уровень.

## <a name="ssl-certificate-issues"></a>Проблемы с SSL-сертификатами

Если вы используете хранилище ключей Java по умолчанию, он уже будет иметь все корневые сертификаты ЦС, и вам не потребуется импортировать дополнительные SSL-сертификаты.

При использовании пользовательского хранилища ключей Java может потребоваться импортировать в него сертификаты SSL Application Insights конечной точки.

### <a name="some-key-terminology"></a>Некоторые ключевые термины:
*Хранилище ключей* — это репозиторий сертификатов, открытых и закрытых ключей. Обычно JDK распределения имеют исполняемый файл для управления ими — `keytool` .

Ниже приведен пример простой команды для импорта SSL-сертификата в хранилище ключей.

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Инструкции по скачиванию и добавлению SSL-сертификата:

1.  Откройте свой любимый браузер и перейдите по `IngestionEndpoint` URL-адресу, указанному в строке подключения, используемой для инструментирования приложения, как показано ниже.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Строка подключения Application Insights":::

2.  Щелкните значок "просмотреть сведения о сайте" (блокировка) в браузере и выберите параметр "сертификат", как показано ниже.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Запись SSL-сертификата":::

3.  Перейдите на вкладку сведения и нажмите кнопку Копировать в файл.
4.  Нажмите кнопку "Далее" и выберите "Base-64 Encoded X. 509 (. CER) "формат и нажмите кнопку" Далее ".

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL-сертификат Експортвизард":::

5.  Укажите файл, в который нужно сохранить SSL-сертификат. Наконец, нажмите кнопку Далее и готово. Вы увидите сообщение "Экспорт выполнен успешно".
6.  Получив сертификат, можно импортировать сертификат в хранилище ключей Java. Используйте приведенную выше [команду](#some-key-terminology) для импорта сертификатов.

> [!WARNING]
> Чтобы получить новый сертификат до истечения срока действия текущего сертификата, необходимо повторить эти действия. Сведения об истечении срока действия можно найти на вкладке "сведения" всплывающего окна сертификата, как показано ниже.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Сведения о SSL-сертификате":::

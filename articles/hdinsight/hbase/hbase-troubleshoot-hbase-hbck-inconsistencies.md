---
title: hbck HBase возвращает несоответствия в Azure HDInsight
description: hbck HBase возвращает несоответствия в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 5fc338e83c172e26d621ef89dcfb047d01d510fa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091691"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Сценарий: `hbase hbck` команда возвращает несоответствия в Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Проблема Регион не находится в`hbase:meta`

Регион XXX в HDFS, но он отсутствует в `hbase:meta` списке или развернут на любом сервере регионов.

### <a name="cause"></a>Причина:

Различать.

### <a name="resolution"></a>Разрешение

1. Исправьте таблицу meta, выполнив команду:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Назначьте регионы для Регионсерверс, выполнив:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Проблема Регион находится вне сети

Регион XXX не развернут ни на одном Регионсервер. Это означает, что регион находится `hbase:meta`в, но вне сети.

### <a name="cause"></a>Причина:

Различать.

### <a name="resolution"></a>Разрешение

Подключите регионы по сети, выполнив:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Проблема Регионы имеют одинаковые ключи начала и окончания

### <a name="cause"></a>Причина:

Различать.

### <a name="resolution"></a>Разрешение

Вручную объединить эти регионы. Перейдите к разделу "Таблица пользовательского веб-интерфейса HBase HMaster" и выберите ссылку на таблицу, в которой возникла эта ошибка. Вы увидите начальный ключ или конечный ключ каждого региона, принадлежащего этой таблице. Затем объедините эти регионы. В оболочке HBase выполните `merge_region 'xxxxxxxx','yyyyyyy', true`. Пример:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

В этом сценарии необходимо объединить Region и Регионк и получить область с тем же диапазоном ключей, что и Регионб, а затем объединить Регионб и регион. XXXXXXX и ииииии — это хэш-строка в конце каждого имени региона. Будьте внимательны, чтобы не выполнять слияние двух несмежных регионов. После каждого слияния, например слияния A и C, HBase запустит сжатие по регионам. Дождитесь завершения сжатия, прежде чем выполнять другое слияние с областью. Сведения о состоянии сжатия можно найти на странице сервера этого региона в пользовательском интерфейсе HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Проблема Не удается загрузить`.regioninfo`

Не удается `.regioninfo` загрузить для `/hbase/data/default/tablex/regiony`региона.

### <a name="cause"></a>Причина:

Скорее всего, это происходит из-за частичного удаления области при Регионсервер сбоях или перезагрузке виртуальных машин. В настоящее время хранилище Azure — это плоская файловая система больших двоичных объектов, а некоторые операции с файлами не являются атомарными.

### <a name="resolution"></a>Разрешение

Вручную очистите оставшиеся файлы и папки:

1. Выполните `hdfs dfs -ls /hbase/data/default/tablex/regiony` , чтобы проверить, какие папки и файлы все еще находятся в нем.

1. Выполните `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` , чтобы удалить все дочерние файлы и папки

1. Выполните `hdfs dfs -rmr /hbase/data/default/tablex/regiony` процедуру, чтобы удалить папку Region.

---

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).

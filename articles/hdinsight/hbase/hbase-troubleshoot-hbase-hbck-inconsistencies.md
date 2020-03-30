---
title: hbase hbck возвращает несоответствия в Azure HDInsight
description: hbase hbck возвращает несоответствия в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887331"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Сценарий: `hbase hbck` команда возвращает несоответствия в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Вопрос: Регион не находится в`hbase:meta`

Регион xxx на HDFS, `hbase:meta` но не указан и не развернут ни на одном сервере региона.

### <a name="cause"></a>Причина

Возможны разные варианты.

### <a name="resolution"></a>Решение

1. Исправьте метатаблицу, запустив:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Назначаем регионы регионам, запуская:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Выпуск: Регион находится в автономном режиме

Регион xxx не развернут ни на одном RegionServer. Это означает, что `hbase:meta`регион находится в, но в автономном режиме.

### <a name="cause"></a>Причина

Возможны разные варианты.

### <a name="resolution"></a>Решение

Переведите регионы в режиме онлайн, запустив:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Выпуск: Регионы имеют одинаковые клавиши начала/конца

### <a name="cause"></a>Причина

Возможны разные варианты.

### <a name="resolution"></a>Решение

Вручную объединить эти перекрываемые области. Перейдите в раздел таблицы веб-страниц HBase HMaster Web, выберите ссылку на таблицу таблицы, которая имеет проблему. Вы увидите ключ-конец/конец каждого региона, относящихся к этой таблице. Затем сливаем эти перекрытые области. В оболочке HBase, делать `merge_region 'xxxxxxxx','yyyyyyy', true`. Пример:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

В этом сценарии необходимо объединить RegionA и RegionC и получить RegionD с тем же ключевым диапазоном, что и RegionB, а затем объединить RegionB и RegionD. xxxxxxxxx и yyyyyy являются хэш строки в конце каждого названия региона. Будьте осторожны здесь, чтобы не объединить два прерывистых регионов. После каждого слияния, например слияния A и C, HBase начнет уплотнение на RegionD. Подождите, пока уплотнение завершится, прежде чем сделать еще одно слияние с RegionD. Статус уплотнения можно найти на странице сервера этого региона в uI HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Выпуск: Не утихнет`.regioninfo`

Не `.regioninfo` утихнет `/hbase/data/default/tablex/regiony`для региона.

### <a name="cause"></a>Причина

Это, скорее всего, связано с частичным удалением региона при сбое regionServer или перезагрузке VM. В настоящее время Хранилище Azure является плоской файловой системой капли, и некоторые файловые операции не являются атомными.

### <a name="resolution"></a>Решение

Вручную очистите эти оставшиеся файлы и папки:

1. Выполните, `hdfs dfs -ls /hbase/data/default/tablex/regiony` чтобы проверить, какие папки / файлы все еще находятся под ним.

1. Выполните, `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` чтобы удалить все файлы ребенка / папки

1. Выполните, `hdfs dfs -rmr /hbase/data/default/tablex/regiony` чтобы удалить папку региона.

---

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)

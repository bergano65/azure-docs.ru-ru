---
title: Настройка SQL для пограничных вычислений (предварительная версия)
description: Сведения о настройке SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594013"
---
# <a name="configure-azure-sql-edge-preview"></a>Настройка SQL для пограничных вычислений (предварительная версия)

SQL Azure для пограничных вычислений поддерживает настройку одним из следующих двух способов:

- Использование переменных среды.
- Использование файла mssql.conf, помещенного в папку /var/opt/mssql

> [!NOTE]
> Задание переменных среды переопределяет параметры, указанные в файле mssql.conf.

## <a name="configure-using-environment-variables"></a>Настройка с помощью переменных среды

SQL Azure для пограничных вычислений предоставляет несколько различных переменных среды, которые можно использовать для настройки контейнера SQL Azure для пограничных вычислений. Эти переменные среды являются подмножеством переменных среды, доступных для SQL Server на Linux. Дополнительные сведения о переменных среды SQL Server на Linux см. в разделе [Переменные среды](/sql/linux/sql-server-linux-configure-environment-variables/).

Следующие переменные среды SQL Server на Linux НЕ поддерживаются для Azure SQL. Эти переменные среды будут игнорироваться во время инициализации контейнера, если их определить.

| Переменная среды | Описание |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Включение группы доступности. Например, значение "1" включает группу, а "0" отключает ее. |

> [!IMPORTANT]
> Переменная среды *MSSQL_PID* для SQL Azure для пограничных вычислений допускает в качестве допустимых значений только **Премиум** и **Разработка**. Azure SQL Server не поддерживает инициализацию с помощью ключа продукта.

> [!NOTE]
> Чтобы скачать лицензионное соглашение SQL для пограничных вычислений для пользователя, ознакомьтесь с [Лицензионным соглашением для пользователей](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Указание переменных среды

Переменные среды для SQL Azure для пограничных вычислений можно указать при развертывании SQL для пограничных вычислений с помощью [портала Azure](deploy-portal.md). Их можно добавить либо в раздел "Переменные среды" развертывания модуля, либо как часть параметра создания контейнера, как описано ниже.

*Установка с помощью параметров переменных среды*

![установка с помощью списка переменных среды](media/configure/set-environment-variables.png)

*Установка с помощью параметров создания контейнера*

![установка с помощью параметров создания контейнера](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Настройка с помощью файла mssql.conf

SQL Azure для пограничных вычислений не включает [служебную программу настройки mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/), как это делает SQL Server на Linux, поскольку файл mssql.conf необходимо вручную настроить и поместить в диск постоянного хранилища, сопоставленный с папкой /var/opt/mssql/ в модуле SQL для пограничных вычислений. При развертывании SQL для пограничных вычислений из Azure Marketplace это сопоставление указывается в качестве параметра **Mounts" (Подключить) в параметре "Создать контейнер".

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Следующие параметры mssql.conf неприменимы к SQL Azure для пограничных вычислений.
</br></br>
|Параметр|Описание|
|:---|:---|
|**Отзывы пользователей** | Включение или отключение отправки отзывов в корпорацию Майкрософт из SQL Server. |
|**Профиль компонента Database Mail** | Настройка профиля Database Mail по умолчанию для SQL Server на Linux. |
|**Высокая доступность** | Включение групп доступности. |
|**Координатор распределенных транзакций Майкрософт** | Настройка координатора распределенных транзакций Майкрософт на платформе Linux. Дополнительные параметры конфигурации, связанные с распределенными транзакциями, также не поддерживаются для SQL Azure для пограничных вычислений. Дополнительные сведения об этих дополнительных параметрах конфигурации см. в разделе [Настройка координатора распределенных транзакций](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**Лицензионные соглашения MLServices** | Примите лицензионные соглашения R и Python для пакетов Службы машинного обучения. Применимо только к SQL Server 2019.|
|**outboundnetworkaccess** |Включение исходящего сетевого доступа для расширений R, Python и Java [Службы машинного обучения](/sql/linux/sql-server-linux-setup-machine-learning/).|

Ниже приведен пример файла mssql.conf, который работает для SQL Azure для пограничных вычислений. Дополнительные сведения о формате файла mssql.conf см. в разделе [Формат mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-step"></a>Следующий шаг

- [Подключение к службе SQL Azure для пограничных вычислений](connect.md)
- [Создание комплексного решения для Интернета вещей с помощью SQL для пограничных вычислений](tutorial-deploy-azure-resources.md)

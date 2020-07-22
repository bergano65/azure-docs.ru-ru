---
title: Настройка SQL для пограничных вычислений (предварительная версия)
description: Узнайте, как настроить Azure SQL (Предварительная версия).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636246"
---
# <a name="configure-azure-sql-edge-preview"></a>Настройка SQL для пограничных вычислений (предварительная версия)

SQL Azure для пограничных вычислений поддерживает настройку одним из следующих двух способов:

- Переменные среды
- Файл MSSQL. conf, помещенный в папку/Вар/ОПТ/мсскл

> [!NOTE]
> Задание переменных среды переопределяет параметры, указанные в файле MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Настройка с помощью переменных среды

SQL Azure для пограничных вычислений предоставляет несколько различных переменных среды, которые можно использовать для настройки контейнера SQL Azure для пограничных вычислений. Эти переменные среды являются подмножеством доступных для SQL Server на Linux. Дополнительные сведения о переменных среды SQL Server на Linux см. в разделе [переменные среды](/sql/linux/sql-server-linux-configure-environment-variables/).

Следующая переменная среды SQL Server на Linux не поддерживается для Azure SQL. Если этот флаг определен, эта переменная среды будет пропущена во время инициализации контейнера.

| Переменная среды | Описание |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Включите группу доступности. Например, **1** включен, а **0** — отключено. |

> [!IMPORTANT]
> Переменная среды **MSSQL_PID** для SQL Azure для пограничных вычислений допускает в качестве допустимых значений только **Премиум** и **Разработка**. Azure SQL Server не поддерживает инициализацию с помощью ключа продукта.

> [!NOTE]
> Скачайте [условия лицензионного соглашения на использование программного обеспечения корпорации Майкрософт](https://go.microsoft.com/fwlink/?linkid=2128283) для Azure SQL.

### <a name="specify-the-environment-variables"></a>Укажите переменные среды

Укажите переменные среды для SQL Server при развертывании службы с помощью [портал Azure](deploy-portal.md). Их можно добавить либо в разделе **переменные среды** в развертывании модуля, либо в составе **параметров создания контейнера**.

Добавьте значения в **переменные среды**.

![Задать с помощью списка переменных среды](media/configure/set-environment-variables.png)

Добавьте значения в **Параметры создания контейнера**.

![Задание с помощью параметров создания контейнера](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Настройка с помощью файла MSSQL. conf

Azure SQL Server не включает в себя [программу настройки MSSQL-CONF](/sql/linux/sql-server-linux-configure-mssql-conf/) , например SQL Server на Linux. Необходимо вручную настроить файл MSSQL. conf и поместить его в постоянный диск хранилища, сопоставленный с папкой/Вар/ОПТ/мсскл/в модуле SQL. При развертывании SQL Server из Azure Marketplace это сопоставление указывается в качестве параметра **Mount** в параметрах **создания контейнера**.

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

Следующие параметры MSSQL. conf не применимы к SQL Server.

|Параметр|Описание|
|:---|:---|
|**Отзывы пользователей** | Выберите, если SQL Server отправляет отзыв в корпорацию Майкрософт. |
|**Профиль компонента Database Mail** | Настройка профиля Database Mail по умолчанию для SQL Server на Linux. |
|**Высокая доступность** | Включение групп доступности. |
|**Координатор распределенных транзакций Майкрософт** | Настройка координатора распределенных транзакций Майкрософт на платформе Linux. Дополнительные параметры конфигурации, связанные с распределенными транзакциями, не поддерживаются для SQL Server. Дополнительные сведения об этих дополнительных параметрах конфигурации см. в разделе [Настройка MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Лицензионные соглашения MLServices** | Примите лицензионные соглашения R и Python для пакетов Машинное обучение Azure. Применимо только к SQL Server 2019.|
|**outboundnetworkaccess** |Включение исходящего сетевого доступа для расширений R, Python и Java [Службы машинного обучения](/sql/linux/sql-server-linux-setup-machine-learning/).|

Следующий пример файла MSSQL. conf работает для SQL Server. Дополнительные сведения о формате файла MSSQL. conf см. в разделе [Формат MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Подключение к службе SQL Azure для пограничных вычислений](connect.md)
- [Создание комплексного решения для Интернета вещей с помощью SQL для пограничных вычислений](tutorial-deploy-azure-resources.md)

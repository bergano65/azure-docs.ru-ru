---
title: Установка TmaxSoft OpenFrame на виртуальных машинах Azure
description: Перехваивайте рабочие нагрузки IBM z/OS с помощью среды TmaxSoft OpenFrame на виртуальных машинах Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436043"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Установка TmaxSoft OpenFrame на Azure

Узнайте, как настроить среду OpenFrame в Azure, подходящую для разработки, демонстраций, тестирования или производственных нагрузок. Этот учебник прогулки вас через каждый шаг.

OpenFrame включает в себя несколько компонентов, которые создают среду эмуляции мэйнфрейма в Azure. Например, онлайн-сервисы OpenFrame заменяют промежуточное программное обеспечение для майнера, такое как IBM Customer Information Control System (CICS), а OpenFrame Batch, с компонентом TJES, заменяет подсистему IBM mainframe's Job Entry (JES).

OpenFrame работает с любой реляционной базой данных, включая базу данных Oracle, сервер Microsoft S'L, IBM Db2 и MyS'L. В этой установке OpenFrame используется реляционная база данных TmaxSoft Tibero. OpenFrame и Tibero работают на операционной системе Linux. Этот учебник устанавливает CentOS 7.3, хотя вы можете использовать другие поддерживаемые дистрибутивы Linux. Сервер приложений OpenFrame и база данных Tibero установлены на одной виртуальной машине (VM).

Учебник проходит через установку компонентов пакета OpenFrame. Некоторые из них должны быть установлены отдельно.

Основные компоненты OpenFrame:

- Необходимые пакеты установки.
- База данных Тиберо.
- Open Database Connectivity (ODBC) используется приложениями в OpenFrame для связи с базой данных Tibero.
- OpenFrame Base, промежуточное программное обеспечение, которое управляет всей системой.
- OpenFrame Batch, решение, которое заменяет пакетные системы мэйнфрейма.
- TACF, модуль обслуживания, который контролирует доступ пользователей к системам и ресурсам.
- ProSort, инструмент сортировки для пакетных транзакций.
- OFCOBOL, компилятор, который интерпретирует программы COBOL мейнфрейма.
- OFASM, компилятор, который интерпретирует программы сборки мейнфрейма.
- OpenFrame Server Type C (OSC), решение, которое заменяет промежуточное программное обеспечение мэйнфрейма и IBM CICS.
- Ява Enterprise User Solution (JEUS), сервер веб-приложений, сертифицированный для Java Enterprise Edition 6.
- OFGW, компонент шлюза OpenFrame, который обеспечивает 3270 слушателя.
- OFManager, решение, которое обеспечивает openFrame функции работы и управления в веб-среде.

Другие необходимые компоненты OpenFrame:

- OSI, решение, которое заменяет промежуточное программное обеспечение мэйнфрейма и IMS DC.
- TJES, решение, обеспечивающее среду JES мейнфрейма.
- OFTSAM, решение, которое позволяет (V)SAM файлы, которые будут использоваться в открытой системе.
- OFHiDB, решение, которое заменяет IMS DB мейнфрейма.
- OFPLI, компилятор, который интерпретирует программы PL/I мейнфрейма.
- PROTRIEVE, решение, которое выполняет язык мэйнфрейма CA-Easytrieve.
- OFMiner— решение, которое анализирует ресурсы мэйнфреймов, а затем переносит их в Azure.

## <a name="architecture"></a>Architecture

Следующая цифра содержит обзор архитектурных компонентов OpenFrame 7.0, установленных в этом учебнике:

![Компоненты OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Требования к системе Azure

В следующей таблице перечислены требования к установке на Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Требование</th><th>Описание</th></tr>
</thead>
<tbody>
<tr><td>Поддерживаемые дистрибутивы Linux на Azure
</td>
<td>
Linux x86 2.6 (32-разрядный, 64-разрядный)<br/>
Красная шляпа 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Оборудование
</td>
<td>Коры: 2 (минимум)<br/>
Память: 4 ГБ (минимум)<br/>
Своп-пространство: 1 ГБ (минимум)<br/>
Твердый диск: 100 ГБ (минимум)<br/>
</td>
</tr>
<tr><td>Дополнительное программное обеспечение для пользователей Windows
</td>
<td>PuTTY: Используется в этом руководстве для настройки функций VM<br/>
WinSCP: популярный клиент SFTP и клиент FTP, который вы можете использовать<br/>
Затмение для Windows: платформа разработки при поддержке TmaxSoft<br/>
(В настоящее время визуальная студия Microsoft не поддерживается)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Предварительные требования

Планируйте потратить несколько дней, чтобы собрать все необходимое программное обеспечение и завершить все ручные процессы.

Прежде чем приначало, сделайте следующее:

- Получите мультимедиа установки OpenFrame от TmaxSoft. Если вы являетесь действующим клиентом TmaxSoft, свяжитесь со своим представителем TmaxSoft для получения лицензионной копии. В противном случае запросите пробную версию у [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Запросить документацию OpenFrame, <support@tmaxsoft.com>отправив электронное письмо.

- Получите подписку Azure, если у вас ее еще нет. Вы также можете создать [бесплатную учетную запись,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) прежде чем начать.

- Необязательный параметр. Настройте VPN-туннель от сайта или прыжок, ограничивающий доступ к VM Azure разрешенным пользователям в вашей организации. Этот шаг не требуется, но это лучшая практика.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Настройка VM на Azure для OpenFrame и Tibero

Можно настроить среду OpenFrame с помощью различных шаблонов развертывания, но следующая процедура показывает, как развернуть сервер приложений OpenFrame и базу данных Tibero на одном VM. В более крупных средах и для значительных рабочих нагрузок наилучшей практикой является развертывание базы данных отдельно на собственном VM для повышения производительности.

**Создание VM**

1. Перейдите на <https://portal.azure.com> портал Azure и войдите в свой аккаунт.

2. Нажмите **Виртуальные машины**.

    ![Список ресурсов на портале Azure](media/vm-01.png)

3. Нажмите кнопку **Добавить**.

    ![Добавление опции на порталAz](media/vm-02.png)

4. Справа от **операционных систем,** нажмите **Подробнее**.

     ![Дополнительная опция на портале Azure](media/vm-03.png)

5. Нажмите **CentOS на основе 7.3,** чтобы следовать этому пошаговой точно, или вы можете выбрать другой поддерживаемый дистрибутив Linux.

     ![Параметры операционной системы на портале Azure](media/vm-04.png)

6. В **настройках Basics** введите **name,** **Имя пользователя,** **тип аутентификации,** **подписку** (Pay-As-You-Go — это стиль оплаты AWS) и **группу ресурсов** (использовать существующую группу или создать группу TmaxSoft).

7. При заполнении (включая публичную/частную ключевую пару для **типа аутентификации),** нажмите **Отправить**.

> [!NOTE]
> При использовании общедоступного ключа SSH для **типа аутентификации,** см. шаги в следующем разделе для создания публичной/ частной пары ключей, а затем возобновите шаги здесь.

### <a name="generate-a-publicprivate-key-pair"></a>Создание публичной/частной ключевой пары

Если вы используете операционную систему Windows, вам нужно PuTTYgen для создания публичной / частной пары ключей.

Открытый ключ может свободно делиться, но частный ключ должен храниться в полной тайне и никогда не должен быть передан другой стороне. После создания ключей необходимо вставить **открытый ключ SSH** в конфигурацию, загрузив его на Linux VM. Он хранится\_внутри авторизованных ключей в каталоге \~/.ssh домашнего каталога учетной записи пользователя. Linux VM затем может распознавать и проверять соединение, как только вы предоставляете связанный **SSH частный ключ** в клиенте SSH (в нашем случае, PuTTY).

Предоставляя новым лицам доступ к ВМ: 

- Каждый новый человек генерирует свои собственные государственные / частные ключи с помощью PuTTYgen.
- Отдельные лица хранят свои собственные личные ключи отдельно и отправляют информацию о ключе общественности администратору VM.
- Администратор вставляет содержимое открытого \~ключа к файлу\_/.ssh/авторизованных ключей.
- Новый человек подключается через PuTTY.

**Для создания публичной/частной ключевой пары**

1.  Скачать PuTTYgen из <https://www.putty.org/> и установить его с помощью всех настроек по умолчанию.

2.  Чтобы открыть PuTTYgen, найдите каталог установки\\PuTTY в C: Program Files\\PuTTY.

    ![Интерфейс PuTTY](media/puttygen-01.png)

3.  Щелкните **Создать**.

    ![Коробка диалога генератора ключей PuTTY](media/puttygen-02.png)

4.  После поколения сохраните как государственный ключ, так и частный ключ. Вставьте содержимое публичного ключа в **разделе SSH public key** панели **«Создание виртуальной \> машины Основы»** (показано на шагах 6 и 7 в предыдущем разделе).

    ![Коробка диалога генератора ключей PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Настройка функций VM

1. На портале Azure в лезвии **«Выберите размер»** выберите нужные настройки оборудования машины Linux. *Минимальные* требования к установке Tibero и OpenFrame составляют 2 процессора и 4 ГБ оперативной памяти, как показано в этом примере установки:

    ![Создание виртуальной машины - Основы](media/create-vm-01.png)

2. Нажмите **3 Настройки** и используйте настройки по умолчанию для настройки дополнительных функций.
3. Просмотрите свои платежные реквизиты.

    ![Создание виртуальной машины - Покупка](media/create-vm-02.png)

4. Отправить свой выбор. Azure начинает развертывать VM. Этот процесс обычно занимает несколько минут.

5. При развертывании VM отображается его панель мониторинга, отображая все параметры, выбранные во время конфигурации. Сделать примечание **общественного IP-адреса**.

    ![tmax на приборной панели Azure](media/create-vm-03.png)

6. Откройте PuTTY.

7. Для **имени хоста**введите имя пользователя и скопированный вами общедоступный IP-адрес. Например, **имя\@пользователя publicip**.

    ![Диалоговый ящик Конфигурации PuTTY](media/putty-01.png)

8. В поле **категории** нажмите **Connection \> SSH \> Auth.** Предоставьте путь к **файлу вашего личного ключа.**

    ![Диалоговый ящик Конфигурации PuTTY](media/putty-02.png)

9. **Нажмите Открыть** для запуска окна PuTTY. В случае успеха вы подключены к новому CentOS VM, работая на Azure.

10. Чтобы войти в систему в качестве корневого пользователя, введите **sudo bash.**

    ![Логин корневого пользователя в окне команды](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Настройка среды и пакетов

Теперь, когда VM создан и вы вошли в систему, необходимо выполнить несколько шагов по настройке и установить необходимые пакеты предустановки.

1. Карта имя **demo** на местный IP-адрес с помощью`vi /etc/hosts`vi для отсеивание хостов файл ( ). Предполагая, что наш IP 192.168.96.148 ofdemo, это до изменения:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Это после изменения:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Создание групп и пользователей:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Измените пароль для пользователя oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Обновление параметров ядра в /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Освежить параметры ядра динамически без перезагрузки:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Получить необходимые пакеты: Убедитесь, что сервер подключен к Интернету, скачать следующие пакеты, а затем установить их:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - nпроклятия

          > [!NOTE]
          > После установки пакета ncurses создайте следующие символические ссылки:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. В случае установки Java RPM сделайте следующее:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Установка базы данных Tibero

Tibero выполняет несколько ключевых функций в среде OpenFrame в Azure:

- Tibero используется в качестве внутреннего хранилища данных OpenFrame для различных системных функций.
- Файлы VSAM, включая KSDS, RRDS и ESDS, используют базу данных Tibero внутри компании для хранения данных.
- Репозиторий данных TACF хранится в Тиберо.
- Информация о каталоге OpenFrame хранится в Тиберо.
- База данных Tibero может быть использована в качестве замены IBM Db2 для хранения данных приложений.

**Для установки Tibero**

1. Убедитесь, что файл установки Tibero присутствует, и просмотрите номер версии.
2. Копирование программного обеспечения Tibero на учетную запись пользователя Tibero (oframe). Пример:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Откройте\_профиль .bash`vi .bash_profile`в vi () и вставьте следующее в ем:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Для выполнения профиля Bash, в типе запроса команды:

    ```
    source .bash_profile
    ```

5. Создайте файл наконечника (файл конфигурации для Tibero), а затем откройте его в vi. Пример:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. \$Измените\_TB HOME/client/config/tbdsn.tbr и положите 127.0.0.1 вместо localhost как показано:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Создайте базу данных. Появится следующий результат:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Чтобы утилизировать Tibero, сначала `tbdown` выключите его с помощью команды. Пример:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Теперь загрузите `tbboot`Tibero используя . Пример:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Для создания рабочего пространства получите доступ к базе данных с помощью пользователя SYS (sys/tmax), а затем создайте необходимое пространство таблицы для громкости по умолчанию и TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Теперь введите следующие команды S'L:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Загрузите Tibero и убедитесь, что процессы Tibero работают:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Выходные данные:

![Выход Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Установка ODBC

Приложения в OpenFrame взаимодействуют с базой данных Tibero с использованием API ODBC, предоставленного проектом unixODBC с открытым исходным кодом.

Для установки ODBC:

1. Убедитесь, что файл установки unixODBC-2.3.4.tar.gz `wget unixODBC-2.3.4.tar.gz` присутствует, или используйте команду. Пример:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Расстегните двоичный файл. Пример:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Перейдите в каталог unixODBC-2.3.4 и создайте Makefile с помощью информации о чековой машине. Пример:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     По умолчанию unixODBC устанавливается в /usr /local, поэтому `--prefix` передает значение для изменения местоположения. Аналогичным образом, файлы конфигурации устанавливаются `--sysconfdir` в /etc по умолчанию, поэтому передает значение желаемого местоположения.

4. Выполнить Makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Копирование исполняемого файла в каталоге программы после компиляции. Пример:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Используйте vi для отсечения профиля Bash ()`vi ~/.bash_profile`и добавить следующее:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Примените ODBC. Отодвите следующие файлы соответственно. Пример:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Создайте символическую ссылку и проверьте подключение базы данных Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Выводится следующий результат.

![Выход ODBC, показывающий, связанный с S'L](media/odbc-01.png)

## <a name="install-openframe-base"></a>Установка базы OpenFrame

Сервер базового приложения устанавливается перед отдельными службами, которые OpenFrame использует для управления системой на Azure, включая процессы обработки транзакций.

**Для установки базы OpenFrame**

1. Убедитесь, что установка Tibero удалось, а\_затем\_\_проверить, что следующие OpenFrame Base7 0 Linux\_x86\_64.bin файл установки и base.properties файл конфигурации присутствуют.

2. Обновление профиля Баш со следующей Информацией, конкретной Tibero:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Выполните профиль Bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Убедитесь, что процессы Tibero запущены. Пример:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Базовая](media/base-01.png)

     > [!IMPORTANT]
     > Убедитесь, что вы начинаете Tibero перед установкой.

5. Создание лицензии на [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) и PUT OpenFrame Base, Batch, TACF, OSC лицензий в соответствующей папке:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Скачать файлы Binary и base.properties OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Выполните установщик с помощью файла base.properties. Пример:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    После завершения, Полное сообщение Установки будет разыграно.

8. Проверьте структуру каталога OpenFrame Base с помощью `ls -ltr` команды. Пример:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Запуск базы OpenFrame:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![выход команды tmboot](media/base-02.png)

10. Проверить состояние процесса готово с помощью команды tmadmin в si. RDY отображается в столбце **состояния** для каждого из процессов:

     ![tmadmin командный выход](media/base-03.png)

11. Выключите базу OpenFrame:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Установка пакета OpenFrame

OpenFrame Batch состоит из нескольких компонентов, имитирующих среды пакетных рамок и используемых для выполнения пакетных заданий в Azure.

**Для установки пакета**

1. Убедитесь, что базовая установка успешно,\_а\_\_затем\_проверить,\_\_что OpenFrame Batch7 0 Fix2 MVS Linux x86\_64.bin файл установки и batch.properties файл конфигурации присутствуют:

2. В запросе команды `vi batch.properties` введите тип для отсеиваем файла batch.properties с помощью vi.

3. Изменить параметры следующим образом:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Для выполнения пакетной установки, в типе запроса команды:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Когда установка будет завершена, запустите установленные `tmboot` наборы OpenFrame, введя в запросе команды.

    ![выход tmboot](media/tmboot-01.png)

6. Введите `tmadmin` запрос команды для проверки процесса OpenFrame.

    ![Экран Tmax Admin](media/tmadmin-01.png)

7. Выполните следующие команды:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Используйте `tmdown` команду для запуска и выключения Batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Установка TACF

TACF Manager — это модуль службы OpenFrame, который контролирует доступ пользователей к системам и ресурсам с помощью безопасности RACF.

**Установка TACF**

1. Убедитесь, что\_OpenFrame\_\_Tacf7\_0\_Fix2\_Linux x86 64.bin файл установки и файл конфигурации tacf.properties присутствуют.
2. Убедитесь, что установка пакета удалось, а затем использовать`vi tacf.properties`vi, чтобы открыть файл tacf.properties ( ).
3. Изменение параметров TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. После завершения установки TACF примените переменные среды TACF. В командной строке введите:

     ```
     source \~/.bash\_profile
     ```

5. Выполните установку TACF. В командной строке введите:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Выходные данные выглядят следующим образом:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. В запросе команды `tmboot` введите тип для перезагрузки OpenFrame. Выходные данные выглядят следующим образом:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Убедитесь, что статус `tmadmin` процесса `si` готов к использованию в команде. Пример:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     В столбце **состояния** отображается RDY:

    ![RDY в столбце состояния](media/tmboot-02.png)

8. Выполните следующие команды:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Выключите сервер `tmdown` с помощью команды. Выходные данные выглядят следующим образом:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Установка ProSort

ProSort — это утилита, используемая в пакетных транзакциях для сортировки данных.

**Для установки ProSort**

1. Убедитесь, что установка Batch была успешной, а затем проверить, что **просорт-бин-просорт\_2sp3-linux64-2123-opt.tar.gz** установщик файл присутствует.

2. Выполните установщик с помощью файла свойств. В командной строке введите:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Переместите каталог просорта в домашнее местоположение. В командной строке введите:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Создайте поддиректор лицензии и скопируйте файл лицензии. Пример:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Откройте bash.profile`vi .bash_profile`в vi ( ) и обновите его следующим образом:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Чтобы выполнить профиль Bash, в запросе команды, введите:`. .bash_profile`

7. Создайте файл конфигурации. Пример:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Создайте символическую ссылку. Пример:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Проверьте установку ProSort, выдыхая `prosort -h` команду. Пример:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Установка OFCOBOL

OFCOBOL — это компилятор OpenFrame, который интерпретирует программы COBOL мейнфрейма. 

**Установка OFCOBOL**

1. Убедитесь, что установка Batch/Online удалась,\_а\_затем\_убедитесь, что файл установки OpenFrame COBOL3 0 40\_Linux\_x86\_64.bin присутствует.

2. Для выполнения установки OFCOBOL, в запросе команды, введите:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Прочитайте лицензионное соглашение и нажмите Enter, чтобы продолжить.

4. Примите лицензионное соглашение. По завершении установки появляется следующее:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Откройте профиль Bash`vi .bash_profile`в vi () и проверьте, что обновляется с переменными OFCOBOL.
6. Выполните профиль Баш. В командной строке введите:

     ```
      source ~/.bash_profile
     ```

7. Копирование лицензии OFCOBOL на установленную папку. Пример:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Перейдите в файл конфигурации OpenFrame tjclrun.conf и откройте его в vi. Пример:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Вот раздел SYSLIB перед изменением:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Вот раздел SYSLIB после изменения:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Просмотрите файл\_OpenFrame COBOL\_InstallLog.log в vi и убедитесь, что ошибок нет. Пример:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Используйте `ofcob --version` команду и просмотрите номер версии для проверки установки. Пример:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Перезагрузка OpenFrame `tmdown/tmboot` с помощью команды.

## <a name="install-ofasm"></a>Установка OFASM

OFASM — это компилятор OpenFrame, который интерпретирует программы ассемблера мейнфрейма.

**Для установки OFASM**

1. Убедитесь, что установка Batch/Online удалась,\_а\_затем\_\_убедитесь,\_что файл установки OpenFrame ASM3 0 Linux x86 64.bin присутствует.

2. Выполните установку. Пример:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Прочитайте лицензионное соглашение и нажмите Enter, чтобы продолжить.
4. Примите лицензионное соглашение.
5. Проверка профиля Bash обновляется с помощью переменных OFASM. Пример:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Откройте файл конфигурации OpenFrame tjclrun.conf в vi и отрежь его следующим образом:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Вот раздел «SYSLIB» *перед* изменением:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Вот раздел «SYSLIB» *после* изменения:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Откройте файл\_OpenFrame ASM\_InstallLog.log в vi и убедитесь, что нет ошибок. Пример:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Перезагрузка OpenFrame, выпустив одну из следующих команд:

     ```
     tmdown / tmboot
     ```

     —или—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Установка OSC

OSC — это среда OpenFrame, аналогичная IBM CICS, которая поддерживает высокоскоростные транзакции OLTP и другие функции управления.

**Для установки OSC**

1. Убедитесь, что базовая установка успешно,\_а\_затем\_проверить,\_что\_OpenFrame OSC7 0 Fix2\_Linux x86 64.bin файл установки и osc.properties файл конфигурации присутствуют.
2. Изыскните следующие параметры в файле osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Выполните установщик с помощью файла свойств, как показано на рисунке:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     После завершения отображается сообщение "Установка полного".

4. Убедитесь, что профиль Bash обновляется переменными OSC.
5. Просмотрите файл\_OpenFrame\_\_OSC7 0 Fix2\_InstallLog.log. Он должен выглядеть примерно так:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Используйте vi, чтобы открыть файл конфигурации ofsys.seq. Пример:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. В \#разделах \#BASE и BATCH отображаем параметры в том виде, в каком они показаны.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Копирование лицензионного файла. Пример:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Чтобы запустить и выключить OSC, инициализируем область `osctdlinit OSCOIVP1` CICS, общая память которой, введя в запросе команды.

10. Запуск `oscboot` для загрузки OSC. Выходные данные выглядят следующим образом:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Чтобы убедиться, что статус процесса `tmadmin` готов, используйте команду в si. Все процессы должны отображать RDY в столбце **состояния.**

    ![Процессы отображения RDY](media/tmadmin-02.png)

12. Выключите OSC `oscdown` с помощью команды.

## <a name="install-jeus"></a>Установка JEUS

JEUS (Решение для пользователей Java Enterprise) предоставляет презентационный слой сервера веб-приложений OpenFrame.

Перед установкой JEUS установите пакет Apache Ant, который предоставляет библиотеки и командные средства, необходимые для установки JEUS.

**Для установки Apache Ant**

1. Скачать Binary `wget` Ant с помощью команды. Пример:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Используйте `tar` утилиту, чтобы извлечь двоичный файл и переместить его в соответствующее место. Пример:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Для повышения эффективности создайте символическую ссылку:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Откройте профиль Bash`vi .bash_profile`в vi ( ) и обновите его со следующими переменными:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Примените модифицированную переменную среды. Пример:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Для установки JEUS**

1. Расширьте установщик с помощью утилиты. `tar` Пример:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Создайте папку **jeus** ()`mkdir jeus7`и распакуйте двоичку.
3. Изменение каталога **настройки** (или использование параметра JEUS для вашей собственной среды). Пример:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Выполните `ant clean-all` перед выполнением сборки. Выходные данные выглядят следующим образом:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Сделайте резервную резервную часть файла domain-config-template.properties. Пример:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Откройте файл домена-конфигурация-template.properties в vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Измените `jeus.password=jeusadmin nodename=Tmaxsoft` на `jeus.password=tmax1234 nodename=ofdemo`.

8. Выполните `ant install` команду для создания JEUS.
9.  Обновление файла\_профиля .bash с переменными JEUS, как показано на рисунке:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Выполните профиль Баш. Пример:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Необязательно*. Создайте псевдоним для легкой остановки и загрузки компонентов JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Чтобы проверить установку, запустите сервер администратора домена в том виде, в каком показано:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Проверить с помощью веб-логона с помощью синтаксиса:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Например, <http://192.168.92.133:9736/webadmin/login.> отображается экран входа:
    
     ![Экран логина JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Если у вас возникли какие-либо проблемы с безопасностью порта,`systemctl stop firewall`откройте порт 9736 или отважите брандмауэр ().

14. Чтобы изменить имя хоста для server1, нажмите **Lock & Edit,** затем нажмите **server1.** В окне Сервера измените имя хоста следующим образом:

    1.  Изменение **названия для ofde** на **ofdemo**.
    2.  Нажмите **OK** на правой стороне окна.
    3.  Нажмите **Применить изменения** на нижней левой стороне окна и для описания, введите *изменение hostname*.

    ![JEUS ВебАдмин экран](media/jeus-02.png)

15. Убедитесь, что конфигурация успешна на экране подтверждения.

    ![экран сервера jeus_domain](media/jeus-03.png)

16. Запустите управляемый процесс сервера "server1" с помощью следующей команды:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Установка OFGW

OFGW является шлюзом OpenFrame, который поддерживает связь между эмулятором терминала 3270 и базой OSI и управляет сеансами между эмулятором терминала и OSI.

**Для установки OFGW**

1. Убедитесь, что JEUS был успешно установлен,\_а\_\_затем убедитесь, что OFGW7 0 1 Generic.bin установщик файл присутствует.
2. Выполните установку. Пример:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Используйте следующие места для соответствующих запросов:
     -   JeUS Главная каталог
     -   Имя домена JEUS
     -   Имя сервера JEUS
     -   Водитель Тиберо
     -   Tmax Node ID де-де-де-

4. Примите остальные по умолчанию, а затем нажмите Enter, чтобы выйти из установки.

5. Убедитесь, что URL-адрес OFGW работает в ожидании:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     На следующем экране отображается:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Установка OFManager

OFManager предоставляет функции работы и управления OpenFrame в веб-среде.

**Для установки OFManager**

1. Убедитесь, что\_файл установки OFManager7 Generic.bin присутствует.
2. Выполните установку. Пример:

     ```
     OFManager7_Generic.bin
     ```

3.  Нажмите Enter продолжить, а затем принять лицензионное соглашение.
4.  Выберите папку установки.
5.  Примите значения по умолчанию.
6.  Выберите Tibero в качестве базы данных.
7.  Нажмите Enter, чтобы выйти из установки.
8.  Убедитесь, что URL-адрес OFManager работает как ожидалось:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Отображается стартовый экран:

![Экран входа в систему Tmax OpenFrame Manager](media/ofmanager-01.png)

Это завершает установку компонентов OpenFrame.

## <a name="next-steps"></a>Дальнейшие действия

Если вы рассматриваете миграцию мэйнфреймов, наша расширяющаяся партнерская экосистема доступна, чтобы помочь вам. См. дополнительные сведение о критериях выбора решений от партнеров в статье [Platform Modernization Alliance](https://datamigration.microsoft.com/) (Альянс модернизации платформы).

-   [Начало работы с Azure](https://docs.microsoft.com/azure/)
-   [Документация по Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Руководство по подкладке в виртуальный центр обработки данных Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)

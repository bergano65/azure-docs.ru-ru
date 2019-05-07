---
title: Установка TmaxSoft OpenFrame на виртуальных машинах Azure
description: Повторное размещение вашей IBM z/OS рабочих нагрузок мэйнфреймов с помощью TmaxSoft OpenFrame среды на виртуальных машинах Azure (ВМ).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: c7e6e7e2023c333207a3a17c3b6711d92de7d044
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65187787"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Установка TmaxSoft OpenFrame в Azure

Узнайте, как настроить среду OpenFrame в Azure подходит для разработки, демонстрации, тестирования или производственных рабочих нагрузок. Этот учебник поможет выполнить каждый шаг.

OpenFrame включает в себя несколько компонентов, которые создают среде эмуляции мэйнфреймов в Azure. Например веб-служб OpenFrame замените по промежуточного слоя мэйнфреймов например IBM клиента сведения управления System (CICS) и OpenFrame пакетной службы, с его компонентом TJES заменяет подсистемы запись задания мэйнфреймов IBM (JES).

OpenFrame работает с любой реляционной базы данных, включая базы данных Oracle, Microsoft SQL Server, IBM Db2 и MySQL. Это установка OpenFrame использует TmaxSoft Tibero реляционной базы данных. OpenFrame и Tibero запустите в операционной системе Linux. Руководстве устанавливается CentOS 7.3, несмотря на то, что можно использовать другие поддерживаемые дистрибутивы Linux. Серверу приложений OpenFrame и Tibero базы данных устанавливаются на одной виртуальной машины (VM).

Руководства шаги по установке компонентов suite OpenFrame. Некоторые должны устанавливаться отдельно.

Компоненты Main OpenFrame:

- Необходимые пакеты установки.
- Tibero базы данных.
- Open Database Connectivity (ODBC) используется приложениями в OpenFrame для взаимодействия с базой данных Tibero.
- Основание OpenFrame, по промежуточного слоя, который управляет всей системы.
- Пакет OpenFrame, решение, которое заменяет ЭВМ пакетной службы.
- TACF модуль службы, который управляет доступом пользователей к системам и ресурсам.
- ProSort средство сортировки пакетные транзакции.
- OFCOBOL, компилятор интерпретирует мэйнфрейме COBOL программы.
- OFASM, компилятор интерпретирует программы ассемблер мэйнфреймов.
- OpenFrame сервера типа C Запускает, решение, которое заменяет по промежуточного слоя и IBM CICS для мэйнфреймов.
- Java Enterprise пользователя решения (JEUS), сервер веб-приложений, который сертифицирован для Java Enterprise Edition 6.
- OFGW OpenFrame компонент шлюза, который предоставляет 3270 прослушиватель.
- OFManager, это решение, обеспечивающее OpenFrame операции и функции управления в среде веб.

Другие необходимые компоненты OpenFrame:

- OSI, решение, которое заменяет по промежуточного слоя для мэйнфреймов и IMS контроллера домена.
- TJES, решение, которое предоставляет среду JES мэйнфреймов.
- OFTSAM, решение, которое позволяет файлы SAM (V) для использования в открытой системы.
- OFHiDB, решение, которое заменяет мэйнфрейма, IMS DB.
- OFPLI, компилятор интерпретирует мэйнфрейма, PL / я программы.
- PROTRIEVE, это решение, которое выполняет языка мэйнфреймов Easytrieve ЦС.
- OFMiner, это решение, которое анализирует ресурсы мэйнфреймов и затем переносит их в Azure.

## <a name="architecture"></a>Архитектура

На следующем рисунке показано с обзором OpenFrame 7.0 архитектурные компоненты, установленные в этом руководстве:

![Компоненты OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Требования к системе Azure

В следующей таблице перечислены требования для установки в Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Требование</th><th>ОПИСАНИЕ</th></tr>
</thead>
<tbody>
<tr><td>Поддерживаемые дистрибутивы Linux в Azure
</td>
<td>
Linux x86 2.6 (32-разрядная, 64-разрядных)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Оборудование
</td>
<td>Ядра: 2 (минимум)<br/>
Память: 4 ГБ (минимум)<br/>
Пространство подкачки: 1 ГБ (минимум)<br/>
Жесткий диск: 100 ГБ (минимум)<br/>
</td>
</tr>
<tr><td>Дополнительное программное обеспечение для пользователей Windows
</td>
<td>PuTTY: В этом руководстве используется для настройки виртуальных машин<br/>
WinSCP: Популярные клиентские SFTP и FTP-клиента, которые можно использовать<br/>
Eclipse для Windows: Это платформа разработки, поддерживаемые TmaxSoft<br/>
(Microsoft Visual Studio не поддерживается в настоящее время)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Технические условия

Запланировать тратить несколько дней, чтобы собрать все необходимое программное обеспечение и завершить все ручные процессы.

Перед началом работы выполните следующие действия.

- Получение установочного носителя OpenFrame из TmaxSoft. Если вы являетесь действующим клиентом TmaxSoft, свяжитесь с представителем TmaxSoft лицензированной копии. В противном случае — запросить пробную версию из [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Запросить OpenFrame документации, отправив сообщение по адресу <support@tmaxsoft.com>.

- Оформление подписки Azure, если у вас еще нет. Вы также можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом работы.

- Необязательный элемент. Настройте VPN-туннель сайт сайт или сервер jumpbox, который ограничивает доступ к виртуальной Машине Azure для разрешенных пользователей в вашей организации. Этот шаг не является обязательным, но рекомендуется.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Настройка виртуальной Машины в Azure для OpenFrame и Tibero

Можно выполнить настройку среды OpenFrame, с помощью различных моделей развертывания, но ниже показано, как развернуть сервер приложений OpenFrame и Tibero базы данных на одной виртуальной Машины. В больших средах, а также для изменяемого размера рабочих нагрузок рекомендуется развернуть базу данных отдельно на отдельной виртуальной Машине для повышения производительности.

**Чтобы создать виртуальную Машину**

1. Перейдите на портал Azure по <http://portal.azure.com> и войдите свою учетную запись.

2. Нажмите **Виртуальные машины**.

    ![Список ресурсов на портале Azure](media/vm-01.png)

3. Щелкните **Добавить**.

    ![Добавить параметр на портале Azure](media/vm-02.png)

4. Справа от **операционных систем**, нажмите кнопку **дополнительные**.

     ![Параметр на портале Azure](media/vm-03.png)

5. Нажмите кнопку **на базе CentOS 7.3** для выполнения этого пошагового руководства точно, или можно выбрать другой поддерживается дистрибутива Linux.

     ![Параметры операционной системы на портале Azure](media/vm-04.png)

6. В **основы** введите параметры, **имя**, **имя пользователя**, **тип проверки подлинности**, **подписки** (Оплата по мере использования — это стиль AWS оплаты), и **группы ресурсов** (использовать существующую или создайте группу TmaxSoft).

7. По завершении процесса (включая пару открытого и закрытого ключей для **тип проверки подлинности**), нажмите кнопку **отправить**.

> [!NOTE]
> Если используется открытый ключ SSH для **тип проверки подлинности**, см. в разделе действия, описанные в следующем разделе для создания пары открытого и закрытого ключей, а затем возобновить при выполнении этих шагов.

### <a name="generate-a-publicprivate-key-pair"></a>Создать пару из открытого и закрытого ключей

Если вы используете операционную систему Windows, необходимо PuTTYgen для создания пары открытого и закрытого ключей.

Открытый ключ можно свободно передаваться, но закрытый ключ должен храниться в полностью секрете и никогда не следует использовать совместно с другой стороны. После создания ключей, необходимо вставить **открытый ключ SSH** в конфигурацию — по сути, передав ее на виртуальную машину Linux. Он хранится внутри авторизованных\_клавиш в \~directory /.ssh домашнего каталога учетной записи пользователя. Виртуальной Машине Linux способен распознавать и проверить подключение, после ввода соответствующего **закрытый ключ SSH** в клиент SSH (в нашем случае PuTTY).

При проведении новых претендентах доступа виртуальной Машины: 

- Каждый новый пользователь создает свои собственные открытого и закрытого ключей, с помощью PuTTYgen.
- Пользователи собственные закрытые ключи хранятся отдельно и отправьте сведения об открытом ключе администратору виртуальной машины.
- Администратор вставляет содержимое буфера открытый ключ, \~/.ssh/authorized\_файл ключей.
- Новый пользователь подключается с помощью PuTTY.

**Для создания пары открытого и закрытого ключей**

1.  Скачайте PuTTYgen из <https://www.putty.org/> и установить его с помощью все параметры по умолчанию.

2.  Чтобы открыть PuTTYgen, перейдите в папку установки PuTTY в папке C:\\Program Files\\PuTTY.

    ![Интерфейс puTTY](media/puttygen-01.png)

3.  Щелкните **Generate** (Создать).

    ![Диалоговое окно генератора ключей puTTY](media/puttygen-02.png)

4.  После создания сохраните открытый ключ и закрытый ключ. Вставьте содержимое файла открытого ключа в **открытый ключ SSH** раздел **Создание виртуальной машины \> основы** области (показано в шагах 6 и 7 в предыдущем разделе).

    ![Диалоговое окно генератора ключей puTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Настройка виртуальных машин

1. На портале Azure в **Выбор размера** колонке выберите параметры оборудования компьютера Linux, вы хотите. *Минимальное* требования к установке Tibero и OpenFrame: 2 процессора и 4 ГБ ОЗУ, как показано в этом примере установке:

    ![Создание виртуальной машины — основы](media/create-vm-01.png)

2. Нажмите кнопку **3 параметров** и используйте параметры по умолчанию для настройки дополнительных компонентов.
3. Просмотрите ваши сведения об оплате.

    ![Создание виртуальной машины - покупки](media/create-vm-02.png)

4. Отправьте выбранные параметры. Azure начнет развертывание виртуальной Машины. Этот процесс обычно занимает несколько минут.

5. При развертывании виртуальной Машины отображается ее панель мониторинга, показывающий все параметры, которые были выбраны во время настройки. Запомните или запишите **общедоступный IP-адрес**.

    ![tmax на панель мониторинга Azure](media/create-vm-03.png)

6. Откройте PuTTY.

7. Для **имя узла**, введите свое имя пользователя и общедоступный IP-адрес можно скопировать. Например **username\@общедоступный IP-адрес**.

    ![Диалоговое окно настройки puTTY](media/putty-01.png)

8. В **категории** выберите **подключения \> SSH \> Auth**. Укажите путь к вашей **закрытый ключ** файл.

    ![Диалоговое окно настройки puTTY](media/putty-02.png)

9. Нажмите кнопку **откройте** для запуска окна PuTTY. В случае успешного выполнения вы подключены к новой виртуальной Машины CentOS в Azure.

10. На вход в качестве привилегированного пользователя, введите **bash, sudo**.

    ![Корневой вход пользователя в окне командной строки](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Настройка среды и пакеты

Теперь, когда виртуальная машина создается, и вход в систему, необходимо выполнить несколько шагов установки и установите необходимые пакеты предварительной установки.

1. Сопоставление имени **ofdemo** локальный IP-адрес с помощью vi, чтобы изменить файл hosts (`vi /etc/hosts`). При условии, что наши IP-адрес является 192.168.96.148 ofdemo, это до изменения:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Это происходит после изменения:

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

3. Изменение пароля для пользователя oframe7.

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Обновите параметры ядра в /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Обновите параметры ядра динамически, без перезагрузки:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Получите необходимые пакеты: Убедитесь, что сервер подключен к Интернету, скачайте следующие пакеты и затем установить их:

     - dos2unix
     - Glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > После установки пакета ncurses, создайте следующий переход по символическим ссылкам:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c ++
     - libaio-devel.x86\_64
     - помощью strace
     - ltrace
     - GDB

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

Tibero обеспечивает несколько ключевых функций в среде OpenFrame по Azure:

- Tibero используется в качестве OpenFrame внутреннего хранилища данных для различных функций системы.
- VSAM файлы, включая KSDS RRDS и ESDS, используют Tibero базы данных для хранения данных.
- Хранилище данных TACF хранится в Tibero.
- Сведения о каталоге OpenFrame хранится в Tibero.
- Tibero базы данных может использоваться для замены IBM Db2 для хранения данных приложений.

**Чтобы установить Tibero**

1. Убедитесь, что файл двоичных установщика Tibero и проверьте номер версии.
2. Скопируйте Tibero программное обеспечение с учетной записью пользователя Tibero (oframe). Например: 

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Откройте .bash\_профиля в vi (`vi .bash_profile`) и вставьте следующий код в нем:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Для выполнения профиля bash, в командной строке введите:

    ```
    source .bash_profile
    ```

5. Создать совет файл (файл конфигурации для Tibero), а затем открыть его в vi. Например: 

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Изменить \$ТБ\_HOME/client/config/tbdsn.tbr и вместо этого поместите 127.0.0.1 oflocalhost, как показано:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Создайте базу данных. Появляется следующий результат:

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

8. Перезапуск Tibero, сначала завершить его работу с помощью `tbdown` команды. Например: 

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Теперь загружаются при помощи Tibero `tbboot`. Например: 

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Чтобы создать табличное пространство, доступ к базе данных, используя SYS пользователя (sys/tmax), затем создайте необходимые табличного пространства для томов по умолчанию и TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Теперь введите следующие команды SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Загрузите Tibero и убедитесь, что процессы Tibero:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Выходные данные:

![Выходные данные Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Установка ODBC

Приложения в OpenFrame взаимодействуют с Tibero базы данных, с помощью ODBC API, предоставляемые unixODBC открытый проект.

Установка ODBC:

1. Убедитесь, что файл unixODBC-2.3.4.tar.gz установщика или использовать `wget unixODBC-2.3.4.tar.gz` команды. Например: 

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Распакуйте двоичного файла. Например: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Перейдите в каталог unixODBC-2.3.4 и сформировать файл Makefile с помощью проверки информация о машине. Например: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     По умолчанию unixODBC устанавливается в/usr/local, поэтому `--prefix` передает значение для изменения расположения. Аналогичным образом, файлы конфигурации устанавливаются в/etc по умолчанию, поэтому `--sysconfdir` передает значение для требуемого расположения.

4. Выполните файл Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Скопируйте исполняемый файл в каталог программы после компиляции. Например: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Использовать vi изменение профиля bash (`vi ~/.bash_profile`) и добавьте следующий код:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Примените ODBC. Соответствующим образом измените следующие файлы. Например: 

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

8. Создание связей и проверки подключения к базе данных Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Выводятся следующие данные:

![ODBC результат, показаны подключения к SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>База OpenFrame

Устанавливается сервер базового приложения, прежде чем отдельных служб, которые OpenFrame использует для управления системой в Azure, включая транзакции, обработка серверных процессов.

**Чтобы установить базовый OpenFrame**

1. Убедитесь, что Tibero установка прошла успешно, а затем убедитесь, что следующие OpenFrame\_Base7\_0\_Linux\_x86\_64. bin установщика и файл конфигурации base.properties присутствуют.

2. Обновление профиля bash следующими данными конкретных Tibero:

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

3. Выполнение профиля bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Убедитесь, что Tibero процессы выполняются. Например: 

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Базовая](media/base-01.png)

     > [!IMPORTANT]
     > Убедитесь, что вы запустили Tibero до установки.

5. Создание лицензии на [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) и ПОМЕСТИТЬ OpenFrame базы, пакетной службы, TACF, лицензирует мастера в соответствующей папке:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Скачайте двоичный файл и base.properties файлы OpenFrame Base:

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

7. Запустить программу установки, с помощью файла base.properties. Например: 

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    По завершении установки полностью будет оснастках.

8. Проверьте структуру каталога OpenFrame базы с помощью `ls -ltr` команды. Например: 

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

9. Базовый OpenFrame начала:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![выходные данные команды tmboot](media/base-02.png)

10. Убедитесь, что состояние процесса = готов, с помощью команды tmadmin в системе Си. RDY отображается в **состояние** столбец для каждого из процессов:

     ![выходные данные команды tmadmin](media/base-03.png)

11. Завершите работу базы OpenFrame:

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

Пакетная служба OpenFrame состоит из нескольких компонентов, которые имитируют среды мэйнфрейма пакетной службы и используется для выполнения пакетных заданий в Azure.

**Для установки пакета**

1. Убедитесь, что базовый установка прошла успешно, а затем убедитесь, что OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_файл установщика 64. bin и файл конфигурации Batch.Properties присутствуют:

2. В командной строке введите `vi batch.properties` внести изменения в файл batch.properties, с помощью vi.

3. Измените параметры следующим образом:

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

4. Для выполнения пакета установщика, в командной строке введите:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. После завершения установки запустите установленных наборов OpenFrame, введя `tmboot` в командной строке.

    ![выходные данные tmboot](media/tmboot-01.png)

6. Тип `tmadmin` в командной строке, чтобы проверить процесс OpenFrame.

    ![Экран Tmax администратора](media/tmadmin-01.png)

7. Выполните следующие команды:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Используйте `tmdown` команду для запуска и завершения работы пакетной службы:

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

Диспетчер TACF — модуль OpenFrame службы, который управляет доступом пользователей к системам и ресурсы с помощью имен пользователей RACF security.

**Чтобы установить TACF**

1. Убедитесь, что OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64. bin установщика и файл конфигурации tacf.properties присутствуют.
2. Убедитесь, что установка пакета, выполнена успешно, а затем используйте vi, чтобы открыть файл tacf.properties (`vi tacf.properties`).
3. Измените параметры TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. После завершения работы установщика TACF, применить TACF переменные среды. В командной строке выполните следующую команду:

     ```
     source \~/.bash\_profile
     ```

5. TACF запустить установщик на выполнение. В командной строке выполните следующую команду:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Результат выглядит примерно следующим образом:

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

6. В командной строке введите `tmboot` перезапустить OpenFrame. Результат выглядит примерно следующим образом:

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

7. Убедитесь, что состояние процесса готов с помощью `tmadmin` в `si` команды. Например: 

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     В **состояние** отображается RDY столбце:

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

9. Завершение работы сервера с помощью `tmdown` команды. Результат выглядит примерно следующим образом:

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

ProSort — это программа, используемые в пакетные транзакции при сортировке данных.

**Чтобы установить ProSort**

1. Убедитесь, что установка пакета прошла успешно и убедитесь, что **prosort bin prosort\_2sp3 linux64-2123 opt.tar.gz** присутствует файл установщика.

2. Запустить программу установки, с помощью файла свойств. В командной строке выполните следующую команду:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Переместите каталог prosort домашнее расположение. В командной строке выполните следующую команду:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Создайте подкаталог лицензии и скопируйте файл лицензии существует. Например: 

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Откройте в vi bash.profile (`vi .bash_profile`) и измените его следующим образом:

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

6. Чтобы выполнить профиля bash, в командной строке введите: `. .bash_profile`

7. Создайте файл конфигурации. Например: 

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Создайте символьную ссылку. Например: 

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Проверка установки ProSort, выполнив `prosort -h` команды. Например: 

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

OFCOBOL является OpenFrame компилятора, который интерпретирует программы мэйнфрейме COBOL. 

**Чтобы установить OFCOBOL**

1. Убедитесь, что установка пакета/Online прошла успешно, а затем убедитесь, что OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_присутствует файл установщика 64. bin.

2. Чтобы запустить программу установки OFCOBOL, в командной строке введите:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Прочитайте лицензионное соглашение и нажмите клавишу ВВОД, чтобы продолжить.

4. Примите лицензионное соглашение. По завершении установки появится сообщение:

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

5. Откройте профиль, bash в vi (`vi .bash_profile`) и убедитесь, что обновляется с переменными OFCOBOL.
6. Выполнение профиля bash. В командной строке выполните следующую команду:

     ```
      source ~/.bash_profile
     ```

7. Скопируйте лицензии OFCOBOL установленных папку. Например: 
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Перейдите к файлу конфигурации tjclrun.conf OpenFrame и откройте его в vi. Например: 
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Ниже приведен в разделе SYSLIB перед изменением.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Ниже приведен в разделе SYSLIB после изменения.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Просмотрите OpenFrame\_COBOL\_InstallLog.log файла в vi и проверьте, что отсутствуют ошибки. Например: 
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
10. Используйте `ofcob --version` команду и проверьте номер версии, чтобы проверить установку. Например: 

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Перезагрузить с помощью OpenFrame `tmdown/tmboot` команды.

## <a name="install-ofasm"></a>Установка OFASM

OFASM является OpenFrame компилятору, что интерпретирует программы ассемблер мэйнфреймов.

**Чтобы установить OFASM**

1. Убедитесь, что установка пакета/Online прошла успешно, а затем убедитесь, что OpenFrame\_ASM3\_0\_Linux\_x86\_присутствует файл установщика 64. bin.

2. Запустить программу установки. Например: 

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Прочитайте лицензионное соглашение и нажмите клавишу ВВОД, чтобы продолжить.
4. Примите лицензионное соглашение.
5. Убедитесь, что с переменными OFASM обновляется профиля bash. Например: 

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

6. Откройте файл конфигурации tjclrun.conf OpenFrame в vi и измените его следующим образом:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Ниже приведен в разделе [SYSLIB] *перед* изменения:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Ниже приведен в разделе [SYSLIB] *после* изменения:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Откройте OpenFrame\_ASM\_InstallLog.log файла в vi и проверьте, что отсутствуют ошибки. Например: 

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

8. Перезагрузите OpenFrame с помощью одной из следующих команд:

     ```
     tmdown / tmboot
     ```

     — или —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Установка мастера

Мастера — это среда OpenFrame, аналогичную IBM CICS, который поддерживает высокоскоростных операций OLTP и другими функциями управления.

**Для установки мастера**

1. Убедитесь, что базовый установка прошла успешно, а затем убедитесь, что OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_файл установщика 64. bin и файл конфигурации osc.properties присутствует.
2. Измените следующие параметры в файле osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Запустить программу установки, с помощью свойства файла, как показано:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     После завершения отображается сообщение «Установка завершена».

4. Убедитесь, что профиль bash обновляется с помощью мастера переменных.
5. Просмотрите OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log файла. Должно отобразиться примерно следующее:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Открыть файл конфигурации ofsys.seq с помощью vi. Например: 

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. В \#BASE и \#ПАКЕТНОЙ разделов и измените параметры, как показано.

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

8. Скопируйте файл лицензии. Например: 

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Для запуска и завершения работы мастера, инициализация памяти регионе совместно CICS, введя `osctdlinit OSCOIVP1` в командной строке.

10. Запустите `oscboot` загружалась мастера. Результат выглядит примерно следующим образом:

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

11. Чтобы убедиться, что состояние процесса готов, используйте `tmadmin` в системе Си. Все процессы должны отображаться RDY в **состояние** столбца.

    ![Отображение RDY процессов](media/tmadmin-02.png)

12. Завершение работы мастера с помощью `oscdown` команды.

## <a name="install-jeus"></a>Установка JEUS

JEUS (решение пользователя Enterprise Java) предоставляет уровень представления сервера OpenFrame веб-приложений.

Прежде чем устанавливать JEUS, установите пакет Apache Ant, который предоставляет библиотеки и средства командной строки, необходимые для установки JEUS.

**Для установки Apache Ant**

1. С помощью двоичного загрузки Ant `wget` команды. Например: 

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Используйте `tar` служебная программа для извлечения двоичный файл и переместите его в соответствующую папку. Например: 

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Для повышения эффективности Создание связей:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Откройте профиль, bash в vi (`vi .bash_profile`) и измените его, указав следующие переменные:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Примените измененную среду переменной. Например: 

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Чтобы установить JEUS**

1. Разверните узел с помощью установщика `tar` служебной программы. Например: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Создание **jeus** папку (`mkdir jeus7`) и извлеките его содержимое двоичного файла.
3. Изменить на **установки** каталог (или используйте параметр JEUS для конкретной среды). Например: 

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Выполнение `ant clean-all` перед выполнением сборки. Результат выглядит примерно следующим образом:

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

5.  Создайте резервную копию файла домена config-template.properties. Например: 

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Откройте файл домена config-template.properties в vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Измените `jeus.password=jeusadmin nodename=Tmaxsoft` на `jeus.password=tmax1234 nodename=ofdemo`.

8. Выполнение `ant install` команду, чтобы создать JEUS.
9.  Обновить .bash\_файл профиля с переменными JEUS, как показано:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Выполнение профиля bash. Например: 

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Необязательный*. Создайте псевдоним для простой завершения работы и загрузки компонентов JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Чтобы проверить установку, запустите сервер администратора домена, как показано:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Проверьте, web входа в систему с использованием синтаксиса:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Например <http://192.168.92.133:9736/webadmin/login.> появится экран входа в систему:
    
     ![Экран входа в систему JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Если возникают проблемы с безопасностью порт, откройте порт 9736 или отключения брандмауэра (`systemctl stop firewall`).

14. Чтобы изменить имя узла для сервера server1, щелкните **заблокировать & Изменить**, затем нажмите кнопку **server1**. В окне сервера измените имя узла следующим образом:

    1.  Изменение **Nodename** для **ofdemo**.
    2.  Нажмите кнопку **ОК** в правой части окна.
    3.  Нажмите кнопку **применить изменения** в нижней левой части окна, а также для описания, введите *изменение имени узла*.

    ![Экран JEUS WebAdmin](media/jeus-02.png)

15. Проверьте успешность выполнения в окне подтверждения конфигурации.

    ![экран jeus_domain сервера](media/jeus-03.png)

16. Начать процесс управляемого сервера «server1», используя следующую команду:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Установка OFGW

OFGW является OpenFrame шлюза, который поддерживает обмен данными между эмулятора терминала 3270 и базы OSI и управляет сеансами между эмулятора терминала и OSI.

**Чтобы установить OFGW**

1. Убедитесь, что JEUS была успешно установлена, а затем убедитесь, что OFGW7\_0\_1\_файл установщика Generic.bin присутствует.
2. Запустить программу установки. Например: 

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Используйте следующие расположения соответствующего инструкциям на экране:
     -   JEUS домашний каталог
     -   Имя домена JEUS
     -   Имя сервера JEUS
     -   Драйвер Tibero
     -   Идентификатор узла Tmax ofdemo

4. Примите остальные значения по умолчанию, а затем нажмите клавишу ВВОД, чтобы закрыть программу.

5. Убедитесь, что URL-адрес для OFGW работает должным образом:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Откроется следующий экран:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Установка OFManager

OFManager предоставляет операции и функции управления для OpenFrame в веб-среду.

**Чтобы установить OFManager**

1. Убедитесь, что OFManager7\_файл установщика Generic.bin присутствует.
2. Запустить программу установки. Например: 

     ```
     OFManager7_Generic.bin
     ```

3.  Нажмите клавишу ВВОД, чтобы продолжить, а затем примите лицензионное соглашение.
4.  Выберите папку установки.
5.  Примите значения по умолчанию.
6.  Выберите Tibero в качестве базы данных.
7.  Нажмите клавишу ВВОД, чтобы закрыть программу.
8.  Убедитесь, что URL-адрес для OFManager работает должным образом:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Отображается рабочий стол:

![Экран входа в систему Tmax OpenFrame Manager](media/ofmanager-01.png)

На этом процесс установки компонентов OpenFrame.

## <a name="next-steps"></a>Дальнейшие действия

Если вы рассматриваете переход с мэйнфреймов, расширяемый экосистеме партнеров помогут вам. См. дополнительные сведение о критериях выбора решений от партнеров в статье [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) (Альянс модернизации платформы).

-   [Приступая к работе с Azure](https://docs.microsoft.com/azure/)
-   [Документация по Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Руководство по Lift-and-Shift в Azure виртуальный центр обработки данных](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)

---
title: Тест-задержка виртуальной сети машин Azure в виртуальной сети Azure Документы Майкрософт
description: Узнайте, как протестировать задержку сети между виртуальными машинами Azure в виртуальной сети
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896361"
---
# <a name="test-vm-network-latency"></a>Проверка задержки сети виртуальной машины

Чтобы достичь наиболее точных результатов, измерьте задержку сети виртуальной машины Azure (VM) с помощью инструмента, предназначенного для выполнения задачи. Общедоступные инструменты, такие как SockPerf (для Linux) и latte.exe (для Windows), могут изолировать и измерять задержку сети, исключая другие типы задержки, такие как задержка приложений. Эти инструменты сосредоточены на виде сетевого трафика, который влияет на производительность приложения (а именно, Протокол управления передачей (TCP) и протокол Datagram пользователя (UDP). 

Другие общие инструменты подключения, такие как Ping, могут измерять задержку, но их результаты могут не представлять сетевой трафик, используемый в реальных рабочих нагрузках. Это связано с тем, что в большинстве этих инструментов используется Протокол управления Интернетом (ICMP), который может рассматриваться иначе, чем трафик приложений, и результаты которого могут не применяться к рабочим нагрузкам, использующим TCP и UDP. 

Для точного тестирования задержки сети протоколов, используемых большинством приложений, SockPerf (для Linux) и latte.exe (для Windows) дают наиболее релевантные результаты. Эта статья охватывает оба этих инструмента.

## <a name="overview"></a>Обзор

Используя два ВМ, один в качестве отправителя и один в качестве приемника, вы создаете двусторонний канал связи. При таком подходе вы можете отправлять и получать пакеты в обоих направлениях и измерять время в оба пути (RTT).

Этот подход можно использовать для измерения задержки сети между двумя вдовым и даже между двумя физическими компьютерами. Измерения задержки могут быть полезны для следующих сценариев:

- Установить ориентир для задержки сети между развернутыми VMs.
- Сравните последствия изменений в задержке сети после внесения соответствующих изменений в:
  - Операционная система (ОС) или программное обеспечение сетевого стека, включая изменения конфигурации.
  - Метод развертывания VM, например развертывание в зоне доступности или группу размещения близости (PPG).
  - Свойства VM, такие как ускоренная сеть или изменение размера.
  - Виртуальная сеть, например, изменение в области реуктора или фильтрации.

### <a name="tools-for-testing"></a>Инструменты для тестирования
Для измерения задержки у вас есть два различных варианта инструмента:

* Для систем на базе Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Для систем на базе Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Используя эти инструменты, вы помогаете гарантировать, что измеряются только время доставки полезной нагрузки TCP или UDP, а не ICMP (Ping) или другие типы пакетов, которые не используются приложениями и не влияют на их производительность.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Советы по созданию оптимальной конфигурации VM

При создании конфигурации VM имейте в виду следующие рекомендации:
- Используйте последнюю версию Windows или Linux.
- Включите ускоренную сеть для достижения наилучших результатов.
- Развертывание ВМ с [группой размещения близости Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Большие VM обычно работают лучше, чем меньшие VMs.

### <a name="tips-for-analysis"></a>Советы по анализу

Анализируя результаты тестов, имейте в виду следующие рекомендации:

- Установить базовый упор на ранней стадии, как только развертывание, конфигурация и оптимизация будут завершены.
- Всегда сравнивайте новые результаты с базовым или, в противном случае, от одного теста к другому с контролируемыми изменениями.
- Повторите тесты всякий раз, когда наблюдаются или планируются изменения.


## <a name="test-vms-that-are-running-windows"></a>Тестовых vMs, работающих под управлением Windows

### <a name="get-latteexe-onto-the-vms"></a>Получить latte.exe на VMs

Скачать [последнюю версию latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Рассмотрите возможность поместить latte.exe в отдельную папку, например, *c:'tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Разрешить latte.exe через Windows Defender firewall

На *приемнике*создайте правило «Разрешить» на брандмауэре Защитника Windows, чтобы трафик latte.exe мог прибыть. Проще всего разрешить всю программу latte.exe по имени, а не разрешать входящие порты TCP.

Разрешить latte.exe через Windows Defender Firewall, запустив следующую команду:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Например, если вы скопировали latte.exe в папку *c:'tools,* это будет команда:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Выполнить тесты задержки

* На *приемнике,* начать latte.exe (запустить его из окна CMD, а не от PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Около 65 000 итераций достаточно долго, чтобы вернуть репрезентативные результаты.

    Любой доступный номер порта в порядке.

    Если у VM есть IP-адрес 10.0.0.4, команда будет выглядеть следующим образом:

    `latte -a 10.0.0.4:5005 -i 65100`

* На *отправителе,* начать latte.exe (запустить его из окна CMD, а не от PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Полученная команда такая же, как на приемнике, за исключением&nbsp;добавления *-c,* чтобы указать, что это *клиент,* или *отправитель:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Дождитесь результатов. В зависимости от того, как далеко друг от друга являются VMs, тест может занять несколько минут, чтобы закончить. Рассмотрите возможность начать с меньшего количества итераций для проверки на успех перед проведением более длительных тестов.

## <a name="test-vms-that-are-running-linux"></a>Тестовых vMs, которые работают Linux

Для тестирования VMs, которые работают Linux, используйте [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Установка SockPerf на vMs

На Linux VMs, как *отправитель* и *приемник*, запустить следующие команды для подготовки SockPerf на VMs. Команды предусмотрены для основных дистро.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Для Red Hat Enterprise Linux (RHEL)/CentOS

Выполните следующие команды:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Для Увунту

Выполните следующие команды:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Для всех дистро

Копировать, компилировать и устанавливать SockPerf в соответствии со следующими шагами:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Выполнить SockPerf на VMs

После завершения установки SockPerf vMs готовы к проведению тестов на задержку. 

Во-первых, начать SockPerf на *приемнике*.

Любой доступный номер порта в порядке. В этом примере мы используем порт 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Теперь, когда сервер слушает, клиент может начать отправку пакетов на сервер в порту, на котором он слушает (в данном случае, 12345).

Около 100 секунд достаточно, чтобы вернуть репрезентативные результаты, как показано в следующем примере:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Дождитесь результатов. В зависимости от того, насколько далеко друг от друга находятся виз, количество итераций будет меняться. Чтобы проверить на успех, прежде чем запустить более длительные тесты, рассмотреть вопрос о начале с более коротких тестов около 5 секунд.

В этом примере SockPerf используется размер сообщения объемом 350 байт, что характерно для среднего пакета. Можно настроить размер выше или ниже, чтобы достичь результатов, которые более точно отражают рабочую нагрузку, которая работает на всай-чах.


## <a name="next-steps"></a>Дальнейшие действия
* Улучшение задержки с [группой размещения близости Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Узнайте, как [оптимизировать сеть для вс-ми](../virtual-network/virtual-network-optimize-network-bandwidth.md) для вашего сценария.
* Читайте о [том, как пропускная способность распределяется до виртуальных машин.](../virtual-network/virtual-machine-network-throughput.md)
* Для получения дополнительной информации, см [Azure Виртуальная сеть часто задаваемые вопросы](../virtual-network/virtual-networks-faq.md).

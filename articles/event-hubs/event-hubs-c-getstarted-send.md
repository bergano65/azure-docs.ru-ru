---
title: Отправка событий в Центры событий Azure с помощью C | Документация Майкрософт
description: Отправка событий в Центры событий Azure с помощью C
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 32345b0f064aa78dbf1cbb84cb2309138e7bf4f7
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455391"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Отправка событий в Центры событий Azure с помощью C

## <a name="introduction"></a>Введение
Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве описано, как отправлять события в концентратор событий с помощью консольного приложения на языке C. 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником требуется:

* Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на виртуальной машине Azure Linux с Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).

## <a name="write-code-to-send-messages-to-event-hubs"></a>Написание кода для отправки сообщений в Центры событий
Из этого раздела вы узнаете, как написать приложение для отправки событий в концентратор на языке C. В коде используется библиотека Proton AMQP из [проекта Apache Qpid](http://qpid.apache.org/). Эта процедура аналогична использованию очередей и разделов службы "Служебная шина" с AMQP на C, как показано [в этом примере](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Дополнительную информацию см. в [документации по Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Чтобы установить Qpid Proton, следуйте инструкциям для своей среды на [странице Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html).
2. Для компиляции библиотеки Proton установите следующие пакеты.
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Скачайте [библиотеку Qpid Proton](http://qpid.apache.org/proton/index.html) и извлеките ее, например:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Создайте каталог построения, скомпилируйте и установите:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. В рабочем каталоге создайте новый файл с именем **sender.c** со следующим кодом. Обязательно замените значения для ключа и имени, имени концентратора событий и пространства имен SAS. Также необходимо заменить версию ключа **SendRule** , закодированную как URL-адрес, созданную ранее. Выполнить кодировку как URL-адрес можно [здесь](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Скомпилируйте файл при условии **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > В этом коде для периода отправки используется значение 1. Это обеспечивают максимальную скорость принудительной отправки сообщений. Рекомендуем сгруппировать сообщения в приложении, чтобы увеличить пропускную способность. Информацию об использовании библиотеки Qpid Proton в этой и других средах, а также на платформах, для которых предоставляются привязки (сейчас это Perl, PHP, Python и Ruby), см. на [странице Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html).

Запустите приложение для отправки сообщений в концентратор событий. 

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать о получении событий из концентратора событий, выберите соответствующий язык получения в узле оглавления **Receive events from an event hub** (Получение событий из концентратора событий).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

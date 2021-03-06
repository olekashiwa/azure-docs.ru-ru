---
title: Руководство по использованию службы "Конфигурация приложений Azure"
description: Руководство по использованию службы конфигурации приложений Azure с приложениями Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 172fe646b294ca511a22128094c56172c4268018
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750276"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения Java Spring с помощью службы "Конфигурация приложений Azure"

В этом кратком руководстве описано, как добавить службу "Конфигурация приложений Azure" в приложение Java Spring, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- Поддерживаемый [комплект разработчика Java (JDK)](https://docs.microsoft.com/java/azure/jdk) версии 8.
- [Apache Maven](https://maven.apache.org/download.cgi) версии 3.0 или более поздней.

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Configuration Explorer** (Обозреватель конфигураций)  >  **+ Создать**, чтобы добавить следующие пары "ключ-значение".

    | Клавиши | Значение |
    |---|---|
    | /application/config.message | Привет |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-spring-boot-app"></a>Создание приложения Spring Boot

Для создания проекта Spring Boot воспользуйтесь [Spring Initializr](https://start.spring.io/).

1. Перейдите по адресу <https://start.spring.io/>.

2. Задайте такие параметры:

   * Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.
   * Выберите **Spring Boot** не ниже версии 2.0.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Добавление зависимости **Spring Web**.

3. После указания предыдущих параметров выберите **Создать проект**. При появлении запроса скачайте проект на локальный компьютер.

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу Конфигурации приложений

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно редактировать. Найдите файл *pom.xml* в корневой папке приложения.

2. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud Azure Config в список `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0</version>
    </dependency>
    ```

3. Создайте файл Java с именем *MessageProperties.java* в каталоге пакета приложения. Добавьте следующие строки.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Создайте файл Java с именем *HelloController.java* в каталоге пакета приложения. Добавьте следующие строки.

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Откройте основной файл приложения Java и добавьте `@EnableConfigurationProperties`, чтобы включить эту функцию.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

6. В каталоге ресурсов создайте файл с именем `bootstrap.properties` и добавьте в него приведенные ниже строки. Замените примеры значений на соответствующие свойства хранилища конфигураций приложений.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```

2. После запуска приложение можно протестировать с помощью средства *curl*, например:

      ```CLI
      curl -X GET http://localhost:8080/
      ```

    Вы увидите сообщение, которое указали в хранилище конфигураций приложений.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано, как создать хранилище конфигураций приложений и использовать его с приложением Java Spring. Дополнительные сведения см. в статье [Spring в Azure](https://docs.microsoft.com/java/azure/spring-framework/). Чтобы узнать, как с помощью удостоверения, управляемого Azure, упростить доступ к службе "Конфигурация приложения Azure", перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)

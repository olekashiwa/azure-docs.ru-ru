---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592895"
---
1. Чтобы запустить функцию, нажмите клавишу **F5**. Кроме того, вам может понадобиться включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы. Уровни авторизации никогда не применяются при локальном запуске.

2. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Локальная среда выполнения Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<YOUR_NAME>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией: 

    ![Ответ функции localhost в браузере](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Нажмите клавиши **SHIFT+F5**, чтобы остановить отладку.
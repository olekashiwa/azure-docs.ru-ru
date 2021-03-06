---
title: Оценка безопасности в центре безопасности Azure | Документы Майкрософт
description: " Определите приоритеты для рекомендаций по безопасности, используя показатель безопасности в центре безопасности Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903446"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Улучшение оценки безопасности в центре безопасности Azure

> [!NOTE]
> В предварительной версии доступен расширенный показатель безопасности. Расширенный показатель безопасности в конечном итоге заменит существующий показатель безопасности, но в то время как он будет выполняться параллельно, чтобы упростить переход.
>
> Дополнительные сведения о преимуществах расширенной оценки безопасности см. [здесь](secure-score-security-controls.md).
>
> Чтобы принять участие в предварительной версии, откройте портал Azure, запустите центр безопасности Azure и выберите "безопасность — оценка". Отсюда вы увидите баннер в верхней части страницы, в которой предлагается новый показатель безопасной работы. Можно также щелкнуть [здесь](https://aka.ms/ascnewscore).

Когда так много служб, предлагающих возможности защиты, порой трудно решить, что следует сделать в первую очередь, чтобы повысить безопасность и надежность рабочих нагрузок. Безопасный рейтинг Azure проверяет ваши рекомендации по безопасности и приоритизирует их для вас, поэтому вы знаете, какие рекомендации выполнять в первую очередь. Это поможет определить, какие уязвимости в системе безопасности являются наиболее серьезными и приоритетными для анализа. Оценка безопасности —это средство, с помощью которого можно оценить состояние безопасности рабочей нагрузки.

## <a name="secure-score-calculation"></a>Вычисление оценки безопасности

Центр безопасности имитирует работу аналитика в сфере безопасности, проверяя рекомендации по безопасности и применяя сложные алгоритмы для определения степени важности каждой из них.
Центр безопасности Azure постоянно изучает ваши активные рекомендации и оценивает вашу оценку безопасности на основе этих рекомендаций. Оценка рекомендаций основана на его серьезности и рекомендациях по обеспечению безопасности, которые влияют на безопасность рабочей нагрузки.

Центр безопасности также предоставляет **общую оценку безопасности**. 

**Общая оценка безопасности** зависит от общей оценки всех рекомендаций. Просмотреть общую оценку безопасности можно для всех подписок или групп безопасности. Она будет зависеть от выбранных подписок и активных рекомендаций в них.

Чтобы узнать, какие рекомендации наиболее сильно влияют на оценку безопасности, можно просмотреть три наиболее важные рекомендации на панели мониторинга центра безопасности или отсортировать рекомендации в колонке по столбцу **Влияние на оценку безопасности**.

Чтобы просмотреть общую оценку безопасности, выполните указанные ниже действия.

1. На панели мониторинга Azure щелкните **Центр безопасности**, а затем щелкните **Оценка безопасности**.

2. В верхней части страницы можно просмотреть особенности оценки безопасности.
   - **Overall secure score** (Общая оценка безопасности) представляет оценку безопасности для политик выбранной подписки.
   - **Secure score by category** (Оценка безопасности по категориям) показывает, какие ресурсы требуют больше всего внимания.
   - **Top recommendations by secure score impact** (Рекомендации с наибольшим влиянием на оценку безопасности) предоставляют список рекомендаций, которые улучшат вашу оценку безопасности, если их придерживаться.
 
   ![оценка безопасности](./media/security-center-secure-score/secure-score-dashboard.png)

3. В следующей таблице показано каждую из ваших подписок и общую оценку безопасности для каждой из них.

   > [!NOTE]
   > Сумма оценки безопасности каждой подписки не равна общей оценке безопасности. Оценка безопасности рассчитывается на основе отношения количества работоспособных ресурсов к общему количеству ресурсов, а не сумме оценок безопасности по вашим подпискам. 
   >
4. Щелкните **Просмотр рекомендаций**, чтобы просмотреть рекомендации для этой подписки, которые можно исправить, чтобы улучшить оценку безопасности.
4. В списке рекомендаций показано, что для каждой рекомендации есть столбец, в котором указано **влияние на оценку безопасности**. Число представляет то, насколько повысится общая оценка безопасности, если выполнить рекомендацию. В приведенном ниже примере, если вы **устраните уязвимости в конфигурациях безопасности контейнеров**, оценка безопасности повысится на 35 баллов.

   ![оценка безопасности](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Отдельные оценки безопасности

Вы также можете просмотреть отдельные оценки безопасности в колонках для отдельных рекомендаций.  

**Оценка безопасности для рекомендации** рассчитывается на основе отношения количества работоспособных ресурсов к общему количеству ресурсов. Если количество работоспособных ресурсов равно общему количеству ресурсов, оценка безопасности для рекомендации будет 50, что является максимальным значением. Чтобы приблизить оценку безопасности к максимальной, исправьте неработоспособные ресурсы, следуя рекомендациям.

**Влияние рекомендации** позволяет узнать, насколько повысится оценка безопасности, если выполнить рекомендацию. Например, если оценка безопасности равна 42, а **влияние рекомендации** равно +3, то выполнение инструкций в рекомендации повысит оценку до 45.

В рекомендации указывается, каким угрозам будет подвергаться рабочая нагрузка, если не выполнить инструкции по исправлению.

![оценка безопасности для отдельной рекомендации](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Дальнейшие действия
В этой статье было рассмотрено, как усилить безопасность с помощью **оценки безопасности** в центре безопасности Azure. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md)— часто задаваемые вопросы об использовании этой службы.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md)— сведения о мониторинге работоспособности ресурсов Azure.



---
description: Принцип разделения интерфейса | Interface Segregation Principle | ISP
---

# ISP

 Много интерфейсов, специально предназначенных для клиентов, лучше, чем один интерфейс общего назначения.

Если в нашем классе C имеется зависимость от какой-то сущности _A_, часть функционала которой \(_A\[unused\]_\) мы не используем, велика вероятность, что при изменениях в _A\[unused\]_  придется внести изменения и в класс C, что нарушает ISP. Такое положение дел плохо тем, что накладывает дополнительные расходы на поддержку системы.

{% hint style="info" %}
Не создавайте зависимостей от того, что не используете.
{% endhint %}


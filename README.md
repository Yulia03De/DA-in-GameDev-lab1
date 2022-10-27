# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнила:
- Дедюхина Юлия Сергеевна
- РИ210930
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.

Ход работы:

- Создала новый пустой 3D проект на Unity.
- Скачала папку с ML агентом. Добавила ML Agent и .json – файлы:

![image](https://user-images.githubusercontent.com/113303734/198079229-6206e4ae-5719-490a-a6d1-997ba68584a0.png)

- Запустила Anaconda Prompt для возможности запуска команд через консоль. Написала серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек: mlagents 0.28.0 (устанавливала версию ниже, т.е. 0.27.0) и torch 1.7.1:

![image](https://user-images.githubusercontent.com/113303734/198079310-1826c85a-00e9-4ab8-b720-a9091060725f.png)
![image](https://user-images.githubusercontent.com/113303734/198079460-49d18ee0-af5a-49d3-acca-44d07606c429.png)
![image](https://user-images.githubusercontent.com/113303734/198079614-4518c476-b9d1-4b63-a859-536260ecd33d.png)

- Создала на сцене плоскость, куб и сферу:

![image](https://user-images.githubusercontent.com/113303734/198079988-cbb09a47-8ce5-445b-b050-227ee56fbf68.png)

C# код:

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}

```

- Добавила компоненты Rigidbody, Decision Requester, Behavior Parameters:

![image](https://user-images.githubusercontent.com/113303734/198080908-fd933fd4-940b-4c64-a8d5-42a6ffe9d10d.png)

- После добавления файла конфигурации нейронной сети в корень проекта, запустила работу ml-агента:

```py

behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000

```

![image](https://user-images.githubusercontent.com/113303734/198081248-c98658df-07e3-4534-9836-625ac4f3ef18.png)

- Проверила работу ML-агента. При запуске программы в консоли выводится следующий код:

![image](https://user-images.githubusercontent.com/113303734/198081716-26ba30d6-a777-420f-8ad3-24d03066cada.png)

- Сделала 6, 9, 27 копий модели «Плоскость-Сфера-Куб»:

6 копий:

![image](https://user-images.githubusercontent.com/113303734/198256218-6431818b-5f4f-4bdf-a718-aa6b9e0666e9.png)

9 копий:

![image](https://user-images.githubusercontent.com/113303734/198256406-9c8a39f5-0146-4d02-be7b-a22d1a373903.png)

27 копий:

![image](https://user-images.githubusercontent.com/113303734/198256577-64a7e106-2ce3-4fe9-b961-adaa83aefa51.png)

- Проверила работу модели:

![image](https://user-images.githubusercontent.com/113303734/198257043-9ad8b3ef-2151-4578-babe-23e114056ca7.png)
![image](https://user-images.githubusercontent.com/113303734/198257392-f141cdf7-65a2-4142-8ded-3a82d1efe0f7.png)

Выводы: При увелечении количества агентов (копий модели) в Unity, модель быстрее обучается, т.е. достигает более высоких показателей точности за меньшее время итераций.

## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.

Код конфигурации нейронной сети:

```py

behaviors: /* Описание поведения объекта */
  RollerBall: /* Имя объекта */
    trainer_type: ppo /* Тип обучения с вознаграждением */
    hyperparameters: /* Гиперпараметры */
      batch_size: 10 /* Количество опытов в за одну итерацию градиентного спуска */
      buffer_size: 100 /* Размер опыта, который необходимо собрать перед обновлением модели политики */
      learning_rate: 3.0e-4 /* Начальная скорость обучения для градиентного спуска */
      beta: 5.0e-4 /* Сила регуляризации энтропии */
      epsilon: 0.2 /* Влияет на быстроту изменения поведения во время обучения */
      lambd: 0.99 /* Параметр регуляризации - лямбда */
      num_epoch: 3 /* Количество проходов через буфер опыта при выполнении оптимизации градиентного спуска */
      learning_rate_schedule: linear /* Параметр изменения скорости обучения с течением времени */
    network_settings: /* Сетевые установки */
      normalize: false /* Параметр, определяющий нормализацию к входным данным */
      hidden_units: 128 /* Количество нейронов в скрытых слоях */
      num_layers: 2 /* Количество скрытых слоёв сети */
    reward_signals: /* Настройки внешних и внутренних сигналов вознаграждения */
      extrinsic: /* Проверка на то, что тренировочный прогон включает в себя сигнал вознаграждения */
        gamma: 0.99 /* Коэффициент поощерения, который должен быть < 1 */
        strength: 1.0 /* Коэффициент силы, на который умножается вознаграждение */
    max_steps: 500000 /* Максимальное количество повторов симуляции сцены */
    time_horizon: 64 /* Количество шагов опыта, которое нужно собрать для каждого агента, хранящихся в буфере до ввода в модель */
    summary_freq: 10000 /* Количество опыта, которое необходимо собрать перед созданием и отображением статистики тренировок по шагам */

```

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости. 

- В созданную сцену добавила еще один куб:

![image](https://user-images.githubusercontent.com/113303734/198274279-cc0b798e-ccd1-42f1-afa0-614061042480.png)

- Доработала C# код:
    
```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public Transform Target2;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        Target2.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(Target2.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);
        float distanceToTarget2 = Vector3.Distance(this.transform.localPosition, Target2.localPosition);

        if(distanceToTarget < 1.42f || distanceToTarget2 < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
    
```
- Изменились параметры Roller Agent:

![image](https://user-images.githubusercontent.com/113303734/198277441-9af4e84a-e254-49d7-91cf-6c080b439d64.png)

- После обучения, проверила работоспособность модели:

![image](https://user-images.githubusercontent.com/113303734/198282332-64a298e9-2818-42c7-86ef-a16527665d3a.png)

## Выводы

В ходе лабораторной работы познакомилась с программными средствами для создания системы машинного обучения и ее интеграции в Unity. 
Игровой баланс - субъективное «равновесие» между персонажами, командами, тактиками игры и другими игровыми объектами. Баланс должен быть и в сюжете, и сложности игры. Для настройки баланса может потребоваться достаточно много времени, поэтому существуют системы машинного обучения, которые помогут поддерживать этот баланс.
Машинное обучение позволяет более точно распределять игровые механики, подлежащие балансу.


| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**

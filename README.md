# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнила:
- Дедюхина Юлия Сергеевна
- РИ210930
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 80 |
| Задание 2 | * | 20 |

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
Интегрировать экономическую систему в проект Unity и обучить ML-Agent.

## Задание 1
### Измените параметры файла. yaml-агента и определить какие параметры и как влияют на обучение модели.

Ход работы:

- Открыла проект Unity, который был доступен в методических указаниях:

![image](https://user-images.githubusercontent.com/113303734/205127678-a915d187-ae27-4334-9833-0b8ca4523964.png)

- В проекте есть скрипт Move.cs, описывающий поведение при движении агента, его вознаграждение и обучение. Он выглядит следующим образом:

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class Move : Agent
{
    [SerializeField] private GameObject goldMine;
    [SerializeField] private GameObject village;
    private float speedMove;
    private float timeMining;
    private float month;
    private bool checkMiningStart = false;
    private bool checkMiningFinish = false;
    private bool checkStartMonth = false;
    private bool setSensor = true;
    private float amountGold;
    private float pickaxeСost;
    private float profitPercentage;
    private float[] pricesMonth = new float[2];
    private float priceMonth;
    private float tempInf;

    // Start is called before the first frame update
    public override void OnEpisodeBegin()
    {
        // If the Agent fell, zero its momentum
        if (this.transform.localPosition != village.transform.localPosition)
        {
            this.transform.localPosition = village.transform.localPosition;
        }
        checkMiningStart = false;
        checkMiningFinish = false;
        checkStartMonth = false;
        setSensor = true;
        priceMonth = 0.0f;
        pricesMonth[0] = 0.0f;
        pricesMonth[1] = 0.0f;
        tempInf = 0.0f;
        month = 1;
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(speedMove);
        sensor.AddObservation(timeMining);
        sensor.AddObservation(amountGold);
        sensor.AddObservation(pickaxeСost);
        sensor.AddObservation(profitPercentage);
    }

    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        if (month < 3 || setSensor == true)
        {
            speedMove = Mathf.Clamp(actionBuffers.ContinuousActions[0], 1f, 10f);
            Debug.Log("SpeedMove: " + speedMove);
            timeMining = Mathf.Clamp(actionBuffers.ContinuousActions[1], 1f, 10f);
            Debug.Log("timeMining: " + timeMining);
            setSensor = false;
            if (checkStartMonth == false)
            {
                Debug.Log("Start Coroutine StartMonth");
                StartCoroutine(StartMonth());
            }

            if (transform.position != goldMine.transform.position & checkMiningFinish == false)
            {
                transform.position = Vector3.MoveTowards(transform.position, goldMine.transform.position, Time.deltaTime * speedMove);
            }

            if (transform.position == goldMine.transform.position & checkMiningStart == false)
            {
                Debug.Log("Start Coroutine StartGoldMine");
                StartCoroutine(StartGoldMine());
            }

            if (transform.position != village.transform.position & checkMiningFinish == true)
            {
                transform.position = Vector3.MoveTowards(transform.position, village.transform.position, Time.deltaTime * speedMove);
            }

            if (transform.position == village.transform.position & checkMiningStart == true)
            {
                checkMiningFinish = false;
                checkMiningStart = false;
                setSensor = true;
                amountGold = Mathf.Clamp(actionBuffers.ContinuousActions[2], 1f, 10f);
                Debug.Log("amountGold: " + amountGold);
                pickaxeСost = Mathf.Clamp(actionBuffers.ContinuousActions[3], 100f, 1000f);
                Debug.Log("pickaxeСost: " + pickaxeСost);
                profitPercentage = Mathf.Clamp(actionBuffers.ContinuousActions[4], 0.1f, 0.5f);
                Debug.Log("profitPercentage: " + profitPercentage);

                if (month != 2)
                {
                    priceMonth = pricesMonth[0] + ((pickaxeСost + pickaxeСost * profitPercentage) / amountGold);
                    pricesMonth[0] = priceMonth;
                    Debug.Log("priceMonth: " + priceMonth);
                }
                if (month == 2)
                {
                    priceMonth = pricesMonth[1] + ((pickaxeСost + pickaxeСost * profitPercentage) / amountGold);
                    pricesMonth[1] = priceMonth;
                    Debug.Log("priceMonth: " + priceMonth);
                }

            }
        }
        else
        {
            tempInf = ((pricesMonth[1] - pricesMonth[0]) / pricesMonth[0]) * 100;
            if (tempInf <= 6f)
            {
                SetReward(1.0f);
                Debug.Log("True");
                Debug.Log("tempInf: " + tempInf);
                EndEpisode();
            }
            else
            {
                SetReward(-1.0f);
                Debug.Log("False");
                Debug.Log("tempInf: " + tempInf);
                EndEpisode();
            }
        }
    }

    IEnumerator StartGoldMine()
    {
        checkMiningStart = true;
        yield return new WaitForSeconds(timeMining);
        Debug.Log("Mining Finish");
        checkMiningFinish = true;
    }

    IEnumerator StartMonth()
    {
        checkStartMonth = true;
        yield return new WaitForSeconds(60);
        checkStartMonth = false;
        month++;

    }
}

```
- Поместила файл Economic.yaml в папку с проектом Unity. Файл является конфигуратором обучающего тренажера:

![image](https://user-images.githubusercontent.com/113303734/205137407-374bc801-2f89-4f08-bdc7-1abc09ab2be8.png)

- Запустила Anaconda Prompt (от имени администратора). Создала виртуальное пространство:

```py

conda create -n TestAgent python==3.6.13

```

![image](https://user-images.githubusercontent.com/113303734/205129525-71444fe3-55a0-4c4f-ba39-6abc727d187a.png)

- Активировала созданное пространство:

```py

conda activate TestAgent

```

![image](https://user-images.githubusercontent.com/113303734/205129829-b436553f-c932-450e-8488-de8125c0a42a.png)

- Установила набор пакетов из третей лабораторной работы:

```py

pip install mlagents==0.28.0

```

![image](https://user-images.githubusercontent.com/113303734/205130410-b6a0f6a6-18fc-4004-804c-8c927d456aa9.png)
![image](https://user-images.githubusercontent.com/113303734/205130460-2b3e0640-dad8-4174-bdca-d00889a65908.png)
![image](https://user-images.githubusercontent.com/113303734/205130547-ff31cb65-2fea-4ed9-8cdf-bb9ba2549232.png)

```py

pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html

```

![image](https://user-images.githubusercontent.com/113303734/205130746-fbb5bd28-6e78-4a60-b01c-856b56325696.png)

- С помощью команды cd перешла в папку с проектом (где лежит Economic.yaml-файл) и запустила обучение ML-агента:

```py

mlagents-learn Economic.yaml --run-id=Economic --force 

```

![image](https://user-images.githubusercontent.com/113303734/205131081-9b299ae0-ac8d-4e76-9b8b-bcfb33211185.png)

При запуске сцены в Unity все работает правильно, в командной строке увидила сообщение о считывании. yaml-файла.

![image](https://user-images.githubusercontent.com/113303734/205131117-ecc0bbe9-0b0f-4162-8425-a9c1cdca0870.png)
![image](https://user-images.githubusercontent.com/113303734/205131153-d0c7197a-47d3-489f-9a4b-8049ffbc6d22.png)
![image](https://user-images.githubusercontent.com/113303734/205131183-eca61eac-ff3a-4337-8017-454842cfc778.png)

- Убедилась, что результаты обучения модели были сохранены. Результаты обучения сохранились в папку с .yaml-файлом: C:\Users\123\Project GitHub DA\MLA_Lab5\MLA_Lab4\results\Economic.

- Построила графики для оценки результатов обучения. Для этого установила TensorBoard:

```py

pip install tensorflow

```

![image](https://user-images.githubusercontent.com/113303734/205133011-b312deba-1a3c-4ad0-966b-657872fdfec9.png)
![image](https://user-images.githubusercontent.com/113303734/205133059-aebbf312-38e5-46b8-bf52-8ba9af83b9ea.png)
![image](https://user-images.githubusercontent.com/113303734/205133095-c2815cf0-7240-461c-a5fb-d5b5555a8064.png)

- После завершения установки запустила TensorBoard:

```py

tensortboard --logdir=results

```

![image](https://user-images.githubusercontent.com/113303734/205133349-a4a76d4a-7c70-40b7-bba6-7a76b99463b1.png)

- По умолчанию TensorBoard запустила по ссылке http://localhost:6006/#scalars:

![image](https://user-images.githubusercontent.com/113303734/205133603-78de8a86-cd2a-4405-9ad2-92fd23bc42f6.png)
![image](https://user-images.githubusercontent.com/113303734/205133631-39f1dd37-ac30-4eb9-ba8e-bbd5b6d1a507.png)

- В начальном запуске были использованы следующие параметры в .yaml файле:

```py

behaviors:
  Economic:
    trainer_type: ppo
    hyperparameters:
      batch_size: 1024
      buffer_size: 10240
      learning_rate: 3.0e-4
      learning_rate_schedule: linear
      beta: 1.0e-2
      epsilon: 0.2
      lambd: 0.95
      num_epoch: 3      
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    checkpoint_interval: 500000
    max_steps: 750000
    time_horizon: 64
    summary_freq: 5000
    self_play:
      save_steps: 20000
      team_change: 100000
      swap_steps: 10000
      play_against_latest_model_ratio: 0.5
      window: 10

```

- Изменила параметры файла yaml-агента следующим образом:

```py

learning_rate: 2.0e-4

```

Значение learning_rate - это начальная скорость обучения для градиентного спуска. Она соответствует силе каждого шага обновления градиентного спуска. Я изменила значение с 3.0e-4 до 2.0e-4. Постепенно линейно уменьшается длина эпизода.
 
```py

epsilon: 0.1

```

Значение epsilon - оно влияет на скорость изменения политики во время обучения. Соответствует допустимому порогу расхождения между старой и новой политикой при обновлении градиентного спуска. Я изменила значение с 0.2 до 0.1. Произошло замедление процесса обучения.

![image](https://user-images.githubusercontent.com/113303734/205136196-73773741-3b25-4961-bdbb-183f7f7690ab.png)
![image](https://user-images.githubusercontent.com/113303734/205136235-569540db-cc9b-40bf-90d1-88e7b43aebac.png)
![image](https://user-images.githubusercontent.com/113303734/205136265-156874b1-0ced-44d1-8972-004342ff893b.png)

http://localhost:6006/#scalars

![image](https://user-images.githubusercontent.com/113303734/205136396-b2528735-a723-456b-a67d-e2822d5da431.png)
![image](https://user-images.githubusercontent.com/113303734/205136418-374c0180-0b1f-4e9f-8887-910ad313e4b4.png)

Исходя из результатов графиков, можно сделать вывод, что изменились графики в Environment, Losses и в некоторых графиках Policy.

## Задание 2
### Опишите результаты, выведенные в TensorBoard.

Ход работы: 

- 

```py



```

-


## Выводы



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

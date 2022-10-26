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
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity. При выполнении задания можно использовать видео-материалы и исходные данные, предоставленные преподавателями курса.

Ход работы:

- Создала новый пустой 3D проект на Unity.
- Скачала папку с ML агентом. Добавила ML Agent и .json – файлы.

![image](https://user-images.githubusercontent.com/113303734/198079229-6206e4ae-5719-490a-a6d1-997ba68584a0.png)

- Запустила Anaconda Prompt для возможности запуска команд через консоль. Написала серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек: mlagents 0.28.0 (устанавливала версию ниже); torch 1.7.1.

![image](https://user-images.githubusercontent.com/113303734/198079310-1826c85a-00e9-4ab8-b720-a9091060725f.png)
![image](https://user-images.githubusercontent.com/113303734/198079460-49d18ee0-af5a-49d3-acca-44d07606c429.png)
![image](https://user-images.githubusercontent.com/113303734/198079614-4518c476-b9d1-4b63-a859-536260ecd33d.png)

- Создала на сцене плоскость, куб и сферу.

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
- Добавление компонентов Rigidbody, Decision Requester, Behavior Parameters:

![image](https://user-images.githubusercontent.com/113303734/198080908-fd933fd4-940b-4c64-a8d5-42a6ffe9d10d.png)

- После добавления файла конфигурации нейронной сети в корень проекта, запускаем работу ml-агента:

![image](https://user-images.githubusercontent.com/113303734/198081248-c98658df-07e3-4534-9836-625ac4f3ef18.png)

- Возвращаемся в Unity и проверяем работу ML-агента. При запуске программы в консоли выводится следующий код:

![image](https://user-images.githubusercontent.com/113303734/198081716-26ba30d6-a777-420f-8ad3-24d03066cada.png)

- Сделала 3, 9, 27 копий модели «Плоскость-Сфера-Куб»:




## Задание 2
### Пошагово выполнить каждый пункт с описанием и примером реализации задачи.
Ход работы: 

- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.
- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

```py

In [ ]:
#Import the required modules, numpy for calculation, and Matplotlib for drawing
import numpy as np
import matplotlib.pyplot as plt
#This code is for jupyter Notebook only
%matplotlib inline

# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)

#Show the effect of a scatter plot
plt.scatter(x,y)

#The basic linear regression model is wx+b, and since this is a two-dimensional space, the model is ax+b
def model(a, b, x):
    return a*x + b

#The most commonly used loss function of linear regresstion model is the loss function of mean, variance difference
def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5/num)*(mss.square(prediction - y)).sum()

#The optimization function mainly USES partial derivatives to update two parameters a and b
def optimize (a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    #Update the values of A and B by finding the partial derivatives of the loss function on a and b
    da = (1.0/num)*((prediction - y)*x).sum()
    db = (1.0/num)*((prediction - y).sum())
    a = a - Lr*da
    b = b - Lr*db
    return a, b

#iterated function, return a and b
def iterate (a, b, x, y, times):
    for i in range (times):
        a, b = optimize(a, b, x, y)
    return a, b

#Initialize parameters and display
a = mss.random.rand(1)
print (a)
b = mss.random.rand(1)
print(b)
Lr = 0.000001

#For the first iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

#For the second iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 2)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

#For the third iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 3)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

#For the fourth iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 4)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

#For the fifth iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 5)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

#For the sixth iteration, the parameter values, losses, and visualization after the iteration are displayed
a, b = iterate(a, b, x, y, 10000)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

```

- Результат программы при 1 итерации:

![image](https://user-images.githubusercontent.com/113303734/192350723-7c25d147-189f-410a-b3c9-32731e36eca6.png)

- Результат программы при 2 итерации:

![image](https://user-images.githubusercontent.com/113303734/192350954-95482382-546d-4e14-9e06-cf2590ab8cb0.png)

- Результат программы при 3 итерации:

![image](https://user-images.githubusercontent.com/113303734/192351175-439e7e8b-5a37-4fef-8ee7-1d94b6bb6492.png)

- Результат программы при 4 итерации:

![image](https://user-images.githubusercontent.com/113303734/192351372-58cdfe4c-12db-446d-93e7-fe67c67c48cb.png)

- Результат программы при 5 итерации:

![image](https://user-images.githubusercontent.com/113303734/192351618-3d03d9e0-fc8c-4f82-b24c-68e7bdd0f4d6.png)

- Результат программы при 6 итерации:

![image](https://user-images.githubusercontent.com/113303734/192351884-7007a776-447a-434d-8875-69fde1d85ab3.png)

## Задание 3
### Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.

Чем больше количество итераций, тем меньше значение loss, которое будет стремиться к нулю.

![image](https://user-images.githubusercontent.com/113303734/192515161-477ff2e2-ad07-45f1-a45f-e97a1905c8b7.png)

### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.

Когда параметр Lr увеличивается, расфокусировка лучей также увеличивается. 
Изменим параметр Lr и посмотрим изменение луча при разных значениях. Изначальное значение в программе было Lr = 0.000001

Результат программы при Lr = 0.000001:

![image](https://user-images.githubusercontent.com/113303734/192508830-4c1d3693-0a4d-4386-9efa-5ce60d4c4acb.png)

Результат программы при Lr = 0.000000001:

![image](https://user-images.githubusercontent.com/113303734/192509276-98473a84-08f9-4ce5-99cc-42b726cf7f6b.png)

Результат программы при Lr = 0.0000008:

![image](https://user-images.githubusercontent.com/113303734/192510375-6a242fa2-9a2f-43a9-acc6-9636e0401467.png)

## Выводы

В ходе лабораторной работы ознакомилась с основными операторами зыка Python на примере реализации линейной регрессии. Также познакомилась с работой в Unity и Google.Colab 

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

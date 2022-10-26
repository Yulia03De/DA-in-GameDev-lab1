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
- Скачала папку с ML агентом. Добавила ML Agent и .json – файлы

![image](https://user-images.githubusercontent.com/113303734/198016614-333c4239-5e48-4bd1-bbca-50d757c1b62d.png)

- Запустила Anaconda Prompt для возможности запуска команд через консоль. Написала серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек: mlagents 0.28.0 (устанавливала версию ниже); torch 1.7.1;




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

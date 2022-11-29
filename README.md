# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнила:
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
Познакомиться с работой перцептрона на практике при помощи Unity. Обучить перцептрон логическим операциям.

## Задание 1
### В проекте Unity реализовать перцептрон, который умеет производить вычисления: OR, AND, NAND, XOR и дать комметрарии о корректности работы.

Ход работы:

- Создала пустой 3D проект Unity.
- В качестве исходных данных был предоставлен скрипт-файл, в котором описана работа перцептрона. 

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}

```
- Реализация операции OR ("Или", дизъюнкция):

![image](https://user-images.githubusercontent.com/113303734/204554448-202d2abe-61af-424f-96a3-7e4ad7217b36.png)

Установила значения для скрипта:

![image](https://user-images.githubusercontent.com/113303734/204541235-d0269809-6b1a-4bcb-b04b-23b57248320c.png)

Значение Total Error отвечает за обучение перцептрона. Если значение больше 0, значит модель не обучилась и следует увеличить количество эпох. Если значение равно 0, значит модель успешно обучилась.

В результате запуска проекта, можно сделать вывод, что перцептрон обучился безошибочно на 5 эпохе:

![image](https://user-images.githubusercontent.com/113303734/204543848-ec75efd9-f1a1-447c-8f4d-fb5741ebfd3d.png)
![image](https://user-images.githubusercontent.com/113303734/204543894-4356968f-c3f1-4993-9ea2-6522d3db622a.png)
![image](https://user-images.githubusercontent.com/113303734/204543971-176f6de6-b4b1-45a5-a58a-d251df394373.png)
![image](https://user-images.githubusercontent.com/113303734/204544005-05022599-c0c8-471e-8f31-1b92e30c17c6.png)
![image](https://user-images.githubusercontent.com/113303734/204544041-658bf84d-44d3-410c-ac64-61753ce83023.png)

- Реализация операции AND ("И", конъюнкция):

![image](https://user-images.githubusercontent.com/113303734/204554352-95713c09-8499-4e84-be5b-7fade1993daa.png)

Установила значения для скрипта:

![image](https://user-images.githubusercontent.com/113303734/204547041-45607f17-8266-4081-8bf8-128d19b84088.png)

В результате запуска проекта, можно сделать вывод, что перцептрон обучился безошибочно также на 5 эпохе:

![image](https://user-images.githubusercontent.com/113303734/204552150-c8d74666-6161-4bb1-9015-657174765f34.png)
![image](https://user-images.githubusercontent.com/113303734/204552325-cd0925a7-2a5d-4716-a37e-3e9779a7ab11.png)
![image](https://user-images.githubusercontent.com/113303734/204552500-f40d1f1b-64dc-4d4a-9ac1-ab0daf11010c.png)
![image](https://user-images.githubusercontent.com/113303734/204552754-ab335653-a567-400a-bcaa-d25f5835de32.png)
![image](https://user-images.githubusercontent.com/113303734/204552899-b98a9870-3c5c-4f1a-bcdc-ccb3c46b3760.png)

- Реализация операции NAND (отрицание конъюнкции):

![image](https://user-images.githubusercontent.com/113303734/204554247-d46cc3dd-9c43-450d-bab1-2181dffc829a.png)

Установила значения для скрипта:

![image](https://user-images.githubusercontent.com/113303734/204556815-65e4f112-1141-475d-be8f-0dae9045ab75.png)

В результате запуска проекта 5 эпох было мало, поэтому я установила значение 6, при котором перцептрон обучился безошибочно:

![image](https://user-images.githubusercontent.com/113303734/204558583-700e9a67-7ac0-49c8-8fd5-41e17a12e20e.png)
![image](https://user-images.githubusercontent.com/113303734/204558695-ea4c84f5-062a-46a2-889b-e1c5c4a39e72.png)
![image](https://user-images.githubusercontent.com/113303734/204558839-e7cad1d9-4111-4fe6-941f-520a2f502712.png)
![image](https://user-images.githubusercontent.com/113303734/204558945-67b718ce-e819-4d9d-8d75-66524aa28cff.png)
![image](https://user-images.githubusercontent.com/113303734/204559161-d4bf0e8a-5986-4506-bb3a-5d86686777b7.png)
![image](https://user-images.githubusercontent.com/113303734/204559250-cde671b0-1fb3-48e2-becb-4058c98f31c3.png)

- Реализация операции XOR (исключающее "Или"):

![image](https://user-images.githubusercontent.com/113303734/204560146-6b267af1-aa4c-48e7-814c-42af93ea663b.png)

Установила значения для скрипта:

![image](https://user-images.githubusercontent.com/113303734/204561325-28301caf-b34e-4743-89f8-726c82f56b0c.png)

При нескольких попытках 6 эпох было мало для безошибочного обучения. Можно сделать вывод, что однослойный перцептрон не может обучиться этой операции, так как однослойные перцептроны способны изучать только линейно разделимые паттерны. Операция XOR не является линейной задачей, поэтому следует добавить еще слои перцептронов.

Результат запуска проекта при 6 эпохах:

![image](https://user-images.githubusercontent.com/113303734/204563889-17da571b-cced-4230-8994-f162d5438b1d.png)

## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.

Ход работы: 



## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.

Ход работы:


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

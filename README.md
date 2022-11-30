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

В результате запуска проекта, можно сделать вывод, что перцептрон обучился безошибочно на 7 эпохе:

![image](https://user-images.githubusercontent.com/113303734/204794170-63091027-1d1c-41fd-9504-366ef8b6b9f2.png)
![image](https://user-images.githubusercontent.com/113303734/204794298-438a5c22-e3e5-4c01-b500-406c302c9e73.png)
![image](https://user-images.githubusercontent.com/113303734/204794392-acf4943f-8faa-4622-8abe-0655f70ddf04.png)
![image](https://user-images.githubusercontent.com/113303734/204794463-77af1a45-25cf-4134-832f-93078ea4a0ee.png)
![image](https://user-images.githubusercontent.com/113303734/204794546-3dfc7267-e99d-408c-ac9d-118b372730c3.png)
![image](https://user-images.githubusercontent.com/113303734/204794625-53eb31f5-72d4-4b9d-9eca-b24b4984030a.png)
![image](https://user-images.githubusercontent.com/113303734/204794747-d7410a91-5bc2-4f25-b531-ede404c74506.png)

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

![image](https://user-images.githubusercontent.com/113303734/204799268-a9864e2f-cbde-4478-ae3a-7cdf6bee7f7f.png)

## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.

Ход работы: 

Каждую операцию я запускала 6 раз с 8 эпохами обучения. 

- Операция OR. График зависимости среднего значения Total Error за 6 попыток от эпохи обучения:

![image](https://user-images.githubusercontent.com/113303734/204801933-7e2eeb67-cada-4e50-b4d2-beec894ecef8.png)

- Операция AND. График зависимости среднего значения Total Error за 6 попыток от эпохи обучения:

![image](https://user-images.githubusercontent.com/113303734/204801786-7b96fd53-583c-470e-90b0-9cc1379f26b4.png)

- Операция NAND. График зависимости среднего значения Total Error за 6 попыток от эпохи обучения:

![image](https://user-images.githubusercontent.com/113303734/204802555-896c9569-6452-42e9-a86c-a083c08c7cce.png)

- Операция XOR. График зависимости среднего значения Total Error за 6 попыток от эпохи обучения:

![image](https://user-images.githubusercontent.com/113303734/204802835-8851d18e-5c25-4dce-9312-7ff5da9830d4.png)

Количество эпох обучения зависит от решаемой задачи. Если в результате работы операции при определенной эпохе возникает ошибка, следует увеличивать значение эпохи.

## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.

Ход работы:

- Создала сцену, в которой будет реализована операция OR. В ней представлены черные и белые кубы. Черные кубы - "1", а белые - "0":

![image](https://user-images.githubusercontent.com/113303734/204837108-ad3a8985-933e-497d-9e00-7716123fff91.png)

- Создала скрипт для смены цвета у объекта при столкновении.

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ChangeColor : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (other.gameObject.GetComponent<Renderer>().material.color == Color.white && this.gameObject.GetComponent<Renderer>().material.color == Color.white)
        {
            other.gameObject.GetComponent<Renderer>().material.color = Color.white;
            this.gameObject.GetComponent<Renderer>().material.color = Color.white;
        }
        else
        {
            other.gameObject.GetComponent<Renderer>().material.color = Color.black;
            this.gameObject.GetComponent<Renderer>().material.color = Color.black;
        }
    }
}

```

- Результат запуска программы:

https://user-images.githubusercontent.com/113303734/204841984-ad189f79-9225-4c77-8f88-34c056565dc6.mp4

## Выводы

В ходе работы познакомилась с работой перцептрона на практике при помощи Unity. Обучила перцептрон логическим операциям OR, AND и NAND. Логической операцией XOR однослойный перцептрон не смог обучиться, так как однослойные перцептроны способны изучать только линейно разделимые задачи. Построила графики зависимости количества эпох от ошибки обучения, а также визуализировала модель работы перцептрона на сцене Unity, в результате чего кубики окрасились в цвета, соответствующие результату логической операции OR.

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

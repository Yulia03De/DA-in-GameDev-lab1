# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнила:
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
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.

Ход работы:

1) В облачном сервисе google console подключить API для работы с google sheets и google drive:

![image](https://user-images.githubusercontent.com/113303734/194915621-561cb5d1-2744-4414-b097-d4836ca7d3fe.png)
![image](https://user-images.githubusercontent.com/113303734/194915678-d1450623-5cd9-4ae3-97e5-a6595f118794.png)
![image](https://user-images.githubusercontent.com/113303734/194916877-4783b050-2e4a-4014-a9f5-7350e242e292.png)

2) Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период:

Код Python:

```py

import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatascience-365111-cfbbafc14689.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[-2]) * 100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sh.sheet1.update(('C' + str(i)), tempInf)
        print(tempInf)

```

![image](https://user-images.githubusercontent.com/113303734/194916112-decf2f76-0e9e-43b4-849f-187227665beb.png)

Google-таблица:

![image](https://user-images.githubusercontent.com/113303734/194916286-4cee10bf-9b19-4ffc-a1d2-31dd28703392.png)

Вывод: данные при выводе в PyCharm и Google-таблицы совпадают.

3) Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте:

Новый проект на Unity:

![image](https://user-images.githubusercontent.com/113303734/194917851-f703f0e9-eadb-4f6e-807d-1f8ea280c1bb.png)

![image](https://user-images.githubusercontent.com/113303734/194917912-7de41a29-75c5-42e5-8671-9fcc52b3cbe8.png)

![image](https://user-images.githubusercontent.com/113303734/194917946-8f65999c-8006-40f3-9de8-1452848ec169.png)

Итоговый код в VS Code:

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1oc2m8TZ7gsLR73cPK7VFnIqN8qr9aJVFcCbSR_11kHo/values/Лист1?key=AIzaSyCncSKgK4l6MmhlgbgjZqd66uGrFUjKOlE");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        Debug.Log(dataSet["Mon_1"]);
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```
Google-таблица:

![image](https://user-images.githubusercontent.com/113303734/194918268-c2227c9f-1793-49ce-9ff0-f837a67ee124.png)

Вывод: данные в консоли Unity и Google-таблицы совпадают.

4) Написать функционал на Unity, в котором будет воспроизводиться аудио-файл в зависимости от значения данных из таблицы:

Подключены аудио-файлы:

![image](https://user-images.githubusercontent.com/113303734/194919066-8fdeae4c-67ec-4047-8a4f-538b0c0b3936.png)

Google-таблица:

![image](https://user-images.githubusercontent.com/113303734/194919127-6d8e492d-57b9-452c-bc7d-46f27c13e010.png)

Вывод: при подключении аудио-файлов данные в консоли Unity и Google-таблицы совпадают.

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 

Ход работы: 

Проделала ту же работу с Google Console как в 1 задании:

![image](https://user-images.githubusercontent.com/113303734/194931381-ba372906-c08b-4c56-b2a5-1e01983b0dbb.png)

Создада лист в google-таблицах по аналогии из 1 задания и переделала код линейной регрессии из ЛР№1, данные появились в таблице:

![image](https://user-images.githubusercontent.com/113303734/194931652-5e9d0e3c-96c6-4849-92d3-4402d5a53b6b.png)

Конечный код:

```py

import gspread
import numpy as np

gc = gspread.service_account(filename='pythonlab1-365117-b8d467fdf2a6.json')
sh = gc.open("PythonSheets_2")

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)

y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

def model(a, b, x):
  return a * x + b

def loss_function(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  return (0.5 / num) * (np.square(prediction - y)).sum()

def optimize(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  da = (1.0 / num) * ((prediction - y) * x).sum()
  db = (1.0 / num) * ((prediction - y).sum())
  a = a - Lr * da
  b = b - Lr * db
  return a, b

def iterate(a, b, x, y, times):
  for i in range(times):
    a, b= optimize(a, b, x, y)
  return a, b

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.0001

prev_loss = 0

for i in range(10):
    a, b = iterate(a, b, x, y, i + 1)

    prediction = model(a, b, x)
    temp_loss = loss_function(a, b, x, y)

    diff_loss = abs(temp_loss - prev_loss)
    prev_loss = temp_loss

    sh.sheet1.update(('A' + str(i + 1)), str(i + 1))
    sh.sheet1.update(('B' + str(i + 1)), str(temp_loss))
    sh.sheet1.update(('C' + str(i + 1)), str(diff_loss))

```

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

Ход работы:

Разработан сценарий, при котором при значении меньше 5 воспроизводится звук Horosho, при значении от 5 до 500 включительно - звук Sredne, а более 500 - звук Ploho.

Скрипт C# в Unity:

```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 5 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 5 & dataSet["Mon_" + i.ToString()] <= 500 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 500 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1oc2m8TZ7gsLR73cPK7VFnIqN8qr9aJVFcCbSR_11kHo/values/Лист1?key=AIzaSyCncSKgK4l6MmhlgbgjZqd66uGrFUjKOlE");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

## Выводы

В ходе лабораторной работы познакомилась с программными средствами для организции передачи данных между инструментами google, Python и Unity. Научилась выводить данные из PyCharm в google-таблицы, а из google-таблиц в Unity с помощью предоставленного видео. Также реализовала запись в google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1 и разработала сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

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

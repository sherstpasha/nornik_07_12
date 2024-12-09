# Кейc для Норникель: Интеллектуальные горизонты (6–8 декабря)

## Задача

На производственных площадках широко применяются алгоритмы компьютерного зрения, однако их эффективность существенно зависит от состояния оборудования. Одной из ключевых проблем является **загрязнение линз камер**, что может привести к снижению точности и корректности работы алгоритмов. Это создает риски для качества автоматического контроля процессов.

### Цель

Разработать решение для **отслеживания загрязнения линз камер**, которое позволит своевременно выявлять проблему и предотвращать негативное влияние на производственные процессы.

---

## Решение

Демонстрация результата через [telegram-бота](https://t.me/N0rNikBot)

![Демонстрация результата через [telegram-бота](https://t.me/N0rNikBot)](demo.gif)

### Технологическая основа

1. **Модель сегментации:** Используется архитектура **YOLO11 Nano**, отличающаяся компактностью и высокой производительностью для задач сегментации в реальном времени.
2. **Данные:** 
   - Для обучения модели создан набор данных, дополненный **синтетическими изображениями**, имитирующими различные типы загрязнений.
   - Применены **аугментации** для повышения устойчивости модели к разнообразным сценариям загрязнения и освещения.

---

# Генерация синтетических изображений для расширения датасета

В нашем репозитории доступен код для создания синтетических изображений, позволяющий генерировать данные с различными эффектами и аугментациями. Это удобно для задач обучения моделей, где требуется искусственно увеличить объём данных или добавить реалистичные визуальные искажения. 

Подробную информацию о процессе и инструкции по использованию вы можете найти в [raindrop_aug.md](raindrop/raindrop_aug.md).

# Шаги для использования `train.ipynb`

#### 1. **Структура ячеек в `train.ipynb`**
Jupyter Notebook (файл `train.ipynb`) состоит из следующих этапов:

1. **Импорты и подготовка путей**
   - Определяются пути к тренировочным и тестовым изображениям и маскам.
   - Создаётся структура директорий для данных в формате YOLO.

2. **Функции для обработки данных**
   - Конвертация масок в формат YOLO (`convert_mask_to_yolo`).
   - Копирование изображений и обработка масок для тренировочного и тестового набора.

3. **Обработка данных**
   - Обрабатываются изображения и маски, сохраняются в соответствующих папках:
     ```
     OUTPUT_DIR/images/train
     OUTPUT_DIR/images/val
     OUTPUT_DIR/labels/train
     OUTPUT_DIR/labels/val
     ```

4. **Создание файла `data.yaml`**
   - Файл `data.yaml` описывает структуру датасета, включая пути и классы.

5. **Обучение модели**
   - Указывается путь к модели (например, `yolo11n-seg.pt`).
   - Настраивается процесс обучения через `model.train`.

---

#### 2. **Как запустить `train.ipynb`**

1. **Откройте файл `train.ipynb`**
   - Откройте Jupyter Notebook:
     ```bash
     jupyter notebook train.ipynb
     ```

2. **Заполните пути**
   - В первой ячейке укажите корректные пути для ваших данных:
     ```python
     IMAGES_TRAIN_DIR = r"C:\path\to\train\images"
     IMAGES_TEST_DIR = r"C:\path\to\test\images"
     MASKS_TRAIN_DIR = r"C:\path\to\train\masks"
     MASKS_TEST_DIR = r"C:\path\to\test\masks"
     OUTPUT_DIR = r"C:\path\to\output"
     ```

3. **Выполните ячейки по порядку**
   - Запускайте ячейки по одной:
     - Первая ячейка: подготовка данных.
     - Следующие ячейки: обработка изображений и создание структуры директорий.
     - Последняя ячейка: запуск обучения.

4. **Проверьте результаты**
   - Убедитесь, что данные обработаны и сохранены в структуре:
     ```
     OUTPUT_DIR/
       ├── images/
       │   ├── train/
       │   └── val/
       ├── labels/
       │   ├── train/
       │   └── val/
       └── data.yaml
     ```
   - После выполнения всех ячеек начнётся обучение модели.

---

#### 3. **Выполнение обучения через Jupyter**
В последней ячейке запускается обучение модели:

```python
model = YOLO("yolo11n-seg.pt")
train_results = model.train(
    data=os.path.join(OUTPUT_DIR, 'data.yaml'),
    epochs=1000,
    imgsz=640,
    device=0,
    patience=500,
)
```

- Модель `yolo11n-seg.pt` используется как базовая.
- Данные берутся из `data.yaml`.

После завершения обучения результаты сохраняются в папке:
```
runs/train/
```

---

### Итог:
- Откройте файл `train.ipynb` в Jupyter Notebook.
- Заполняйте пути в первой ячейке.
- Выполняйте ячейки по порядку, завершая на этапе обучения.
- Проверьте сохранённые результаты.

# README: Генерация Submit для модели

Данный [скрипт](script_submission/infer_by_test.py) предназначен для инференса модели и создания файла submit, содержащего результаты в формате JSON.

## Описание работы скрипта

### Входные данные:
1. **Путь к тестовому датасету** – директория, содержащая изображения в формате `.jpg`. Название директории передается в аргументах командной строки.
2. **Путь к выходному файлу** – полный путь к файлу, куда будет сохранен submit в формате JSON. Указывается в аргументах командной строки.

### Выходные данные:
- Файл `submit.json`, содержащий результаты инференса:
  - **Ключи:** названия файлов изображений.
  - **Значения:** маски, закодированные в формате Base64.

### Особенности:
- Маски создаются на основе инференса модели.
- Все результаты сохраняются в один файл `submit.json`.

---

## Использование

### Запуск скрипта:

1. Убедитесь, что все файлы находятся в одной директории и имеют корректные относительные пути.
2. Запустите скрипт с указанием двух параметров:
   - Путь к тестовому датасету.
   - Путь к выходному файлу.

Пример команды:
```bash
python script_submission/infer_by_test.py /path/to/test_dataset /path/to/output_directory
```

---

## Описание логики работы

1. **Загрузка модели:**
   - Скрипт загружает модель из файла `baseline.pt`.
   - Обратите внимание, что модель должна быть включена в архив проекта, так как доступ к интернету будет отсутствовать.

2. **Инференс:**
   - Каждое изображение из тестового датасета проходит обработку моделью.
   - Результаты инференса используются для создания бинарной маски.

3. **Создание масок:**
   - Маски создаются на основе результатов модели и преобразуются в формат PNG.
   - Каждая маска кодируется в строку Base64.

4. **Сохранение результатов:**
   - Все маски накапливаются в словаре с названием изображения в качестве ключа.
   - После обработки всех изображений результаты сохраняются в файл `submit.json`.

---

## Замечания

- **Неизменность логики:** Логика работы скрипта полностью соответствует исходному скрипту от кейсодержателя. Изменения вносить не требуется.
- **Требования к окружению:**
  - Убедитесь, что библиотека `ultralytics` и зависимости (например, `cv2`, `numpy`) установлены.
  - Все пути к файлам и директориям должны быть указаны относительно корневого каталога проекта.
- **Модель:** Убедитесь, что файл модели `baseline.pt` находится в архиве проекта.

---

# Оптимизация параметров пред- и постобработки для улучшения mean IoU

### Описание

Данный проект предназначен для автоматической оптимизации параметров, связанных с предобработкой изображений и постобработкой масок сегментации, с целью максимизации метрики **mean IoU** между предсказанными и истинными масками. 

Оптимизация параметров осуществляется с помощью алгоритма **дифференциальной эволюции**, который позволяет найти лучшие значения для:
- **Яркости (brightness_factor)**: настройка яркости изображений.
- **Контраста (contrast_factor)**: усиление или ослабление контрастности.
- **Шумоподавления (noise_factor)**: подавление шумов с помощью фильтров.
- **Масштабирования контуров (contour_scale)**: увеличение или уменьшение размера контуров в предсказанных масках.
- **Сглаживания контуров (epsilon_factor)**: сглаживание углов и неровностей контуров.

---

### Как пользоваться

1. **Запуск оптимизации:**
   - Убедитесь, что ваши изображения и маски находятся в соответствующих папках:
     - `dataset_path`: Путь к изображениям.
     - `gt_path`: Путь к истинным маскам (Ground Truth).
   - Задайте путь к модели:
     - `model_path`: Укажите путь к вашей модели YOLOv8.

   - Запустите скрипт `optimize_prep.py`:
     ```bash
     python optimize_prep.py
     ```

2. **Результаты:**
   - После выполнения скрипта в консоли отобразятся лучшие параметры:
     ```text
     Лучшие параметры: [1.2, 1.3, 0.4, 1.15, 0.02]
     Достигнутый mean IoU: 0.85
     ```

   - Эти параметры можно использовать в дальнейшем для обработки изображений и создания масок.

3. **Использование лучших параметров:**
   - Добавьте параметры в свою основную программу. Например:
     ```python
     best_brightness = 1.2
     best_contrast = 1.3
     best_noise = 0.4
     best_contour_scale = 1.15
     best_epsilon = 0.02
     ```
   - Используйте их в функциях `infer_by_test_prep.py`.

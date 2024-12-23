# Инструкция по использованию скрипта для аугментации изображений и масок

## Описание
Скрипт предназначен для генерации заданного количества изображений и соответствующих масок с применением случайных аугментаций. Он автоматически распознает маски для изображений, создает недостающие маски, а также поддерживает параллельную обработку для повышения производительности.

## Требования
1. Python 3.6+
2. Установленные библиотеки:
   - `Pillow`
   - `numpy`
   - `joblib`

Установить зависимости можно командой:
```bash
pip install pillow numpy joblib
```

## Настройка
1. Задайте входные данные:
   - Папка с изображениями: `INPUT_IMAGE_FOLDER`
   - Папка с масками: `INPUT_MASK_FOLDER`
   - Папка для сохранения выходных данных: `OUTPUT_FOLDER`

2. Укажите параметры:
   - `NUM_IMAGES_TO_GENERATE` — количество изображений, которое нужно сгенерировать.
   - `N_JOBS` — количество потоков для параллельной обработки.

Пример настройки:
```python
INPUT_IMAGE_FOLDER = r"C:\path\to\images"
INPUT_MASK_FOLDER = r"C:\path\to\masks"
OUTPUT_FOLDER = r"C:\path\to\output"
NUM_IMAGES_TO_GENERATE = 1000
N_JOBS = 4
```

## Как работает скрипт
1. **Поиск масок**:
   - Для каждого изображения ищется маска с таким же именем и одним из расширений (`.png`, `.jpg`, `.jpeg`).
   - Если маска не найдена, создается пустая маска того же размера.

2. **Аугментация**:
   - На каждое изображение случайно применяются следующие трансформации:
     - Поворот на 90°, 180° или 270°.
     - Отражение по горизонтали или вертикали.
     - Изменение яркости.
     - Изменение контраста.

3. **Сохранение результатов**:
   - Все обработанные изображения и маски сохраняются в отдельные папки:
     - `images/` — аугментированные изображения.
     - `masks/` — объединенные маски.

4. **Параллельная обработка**:
   - Для ускорения обработки используется многопоточная обработка с помощью библиотеки `joblib`.

## Запуск
1. Убедитесь, что все пути и параметры указаны корректно.
2. Запустите скрипт:
   ```bash
   python script_name.py
   ```
3. После завершения обработки результаты будут сохранены в папке `OUTPUT_FOLDER`.

## Структура папок после выполнения
```plaintext
output/
├── images/
│   ├── image_0001.png
│   ├── image_0002.png
│   └── ...
├── masks/
│   ├── mask_0001.png
│   ├── mask_0002.png
│   └── ...
```

## Замечания
- Убедитесь, что изображения и маски имеют соответствующие размеры, чтобы избежать ошибок при комбинировании.
- Параметр `N_JOBS` следует подбирать в зависимости от количества доступных процессоров и объема памяти.

## Пример вывода
При успешном выполнении в консоли будет выведено:
```
Обработка завершена! Создано 1000 изображений.
```

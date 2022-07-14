# Virtual environment for Python

Установка:
```
sudo apt get install python3-venv
```

Создание виртуального окружения:
```
путь/до/интерпретатора/bin/python -m venv venv370

# или:
путь/до/интерпретатора/bin/python -m venv --without-pip --system-site-packages venv370

# или просто:
virtualenv myvenv
```

Активируем виртуальное окружение:
```
source venv370/bin/activate
```

Установка в виртуальное окружение необходимых зависимостей:
```
python -m pip install --upgrade pip
python -m pip install -r requirements.txt

# или просто:
pip install django
```

Если нужен jupyter-kernel:
```
python -m ipykernel install --user --name=venv370
```

Проверка версий библиотек и получение полного списка зависимостей:
```
pip freeze | grep django

pip freeze > requrements.txt
```

Деактивируем виртуальное окружение:
```
deactivate
```

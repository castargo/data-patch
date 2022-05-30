# DVC cheat sheets

### Setting up

Версия:       
```dvc -V```
 
Инициализация:        
```dvc init (или dvc init --subdir)```
 
Статус:    
```dvc status```

Создание удаленного хранилища:       
```hdfs dfs -mkdir hdfs://path/to/data/```
 
Добавление прав на директорию (например, 777):      
```hdfs dfs -chmod 777 hdfs://path/to/data/```
 
Инициализация удаленного хранилища:     
```dvc remote add -d hdfs hdfs://path/to/data/```
 
Запуск пайплайна dvc.yaml:     
```dvc repro```
 
Запись в удаленное хранилище файлов, содержащихся в секции outs:          
```dvc push```

Удаление кэша экспериментов:          
```rm -rf .dvc/cache```

Запуск garbage collector:         
```dvc gc```

Восстановление удаленных файлов из удаленного хранилища (по файлу dvc.lock):             
```dvc pull```

Переключение между версиями (по файлу dvc.lock):         
```dvc checkout```

Получившийся граф dvc-pipeline:         
```dvc dag```
 
Заморозим запуск одного из шагов dvc.yaml:       
```dvc freeze stage_number_one```
 
А теперь снова разморозим:       
```dvc unfreeze stage_number_one```


### dvc.yaml

```
stages:
 # 1. First stage
 stage_number_one:
  cmd: path/to/.../bin/python my_awesome_python_script.py
  deps:
  - input/data.pkl
  - path/to/.../bin/python my_awesome_python_script.py
  params:
  - output_folder
  - model.random_state
  - target_features_path
  outs:
  - output/dvc_result/train_df.pkl
  - output/dvc_result/test_df.pkl
```

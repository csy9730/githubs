# DeepLearningStudio

[TOC]

## Entry

服务器入口是：

C:\Program Files\DeepLearningStudio\manager\deep-learning-studio-manager.exe

网页入口是：

http://127.0.0.1:8880/login/
http://127.0.0.1:8880/app/



其他页面是：

http://127.0.0.1:8880/app/projects

http://127.0.0.1:8888/?deepcognition&1

## main

### overview

``` tree
.
├── 7z
├── bin
├── cmd
├── conda3
├── data
├── datasets_scripts
├── dlenvs
├── etc
├── home
├── manager
├── mingw64
├── mxnet
├── nginx
├── php
├── public
├── tmp
├── usr
└── var
```



* 7z, 简单目录，包含7z文件
* bin： 简单目录包含git提供的bash/git/sh
* cmd： 简单目录包含git/gitk/git-gui
* conda3： 复杂目录，包含conda的安装目录的所有文件
* data
  * public
    * dlenvs
    * datasets
* datasets_scripts 简单目录包含install_pydatasets.py
* dlenvs
* etc 一些配置文件
* home
  * app
  * olympus
* manager
* mingw64
* mxnet
* nginx
* php
* public



### dlenvs

`$ E:\Program Files\DeepLearningStudio\data\1\.conda\Scripts\conda-env-script.py create -f C:\Users\admin\AppData\Local\Temp\tmpgnpr0hr8/env.yml -n mxnet-1.1.0b`

### dataset

C:\Program Files\DeepLearningStudio\data\public\datasets



``` bash
E:.
├─cifar-10
│  └─images
│      ├─0
│      └─9
├─imdb
│      .status.json
│      meta.json
│      README.txt
│      train.csv
├─iris
│      .status.json
│      meta.json
│      README.txt
│      train.csv
│
├─mnist
│      .log.txt
│      .status.json
├─reuters
│      .log.txt
│      .status.json
│      meta.json
│      train.csv
└─titanic
        .status.json
        meta.json
        README.txt
        train.csv
```


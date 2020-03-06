---
title: Qt控件
date: 2020-03-02 00:00:28
tags:
---

## 输入框Lineedit
+ 设置ip输入
```
QRegExp rx("((1?\\d{1,2}|2[0-5]{2})\\.){3}(1?\\d{1,2}|2[0-5]{2})");
QValidator * validator = new QRegExpValidator(rx, this);
ui->lineEdit->setValidator(validator);
```

<!-- more -->
## QTablewidget

```
//添加项目
table->setItem(i,0,new QTableWidgetItem(namelist[i]));
//设置列
_table->setColumnCount(size);
//设置标题
_table->setHorizontalHeaderLabels(QStringList);
//设置大小
_table->horizontalHeader()->setDefaultSectionSize(200);
//设置选择行为时每次选择一行
_table->setSelectionBehavior(QAbstractItemView::SelectRows);
//设置不可编辑
_table->setEditTriggers(QAbstractItemView::NoEditTriggers); 
//设置选中背景色
_table->setStyleSheet("selection-background-color:lightblue;"); 
```

## QTreeWidget

```
//删除节点
    while(tree->topLevelItemCount()>0){//删除树节点  未全部释放
        tree->takeTopLevelItem(0);
    }
```

## QPushbutton

```
//设置回车绑定
button->setShortcut(Qt::Key_Return);

```
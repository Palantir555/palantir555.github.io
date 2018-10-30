---
layout: post
title: Creating a Drop-Down Menu with Qt 4.8
tags:
- software development
- c++
- qt
- drop-down
- gui
---

Using the widget QComboBox in Qt 4.8 is pretty easy, but the documentation can
be a little bit confusing the first time you want to use it, so here is a quick
example on how to use its basic features:

{% highlight c++ %}
#include <QtGui/QApplication>
#include "mainwindow.h"
int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    MainWindow *w = new MainWindow();
    w->show();
    
    return a.exec();
}
{% endhighlight %}

{% highlight c++ %}
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QtGui/QDialog>
#include <QComboBox>
#include <QLabel>
#include <QGridLayout>

class MainWindow : public QDialog
{
    Q_OBJECT
public:
    MainWindow(QWidget *parent = 0);

private:
    QGridLayout *myGrid;

    //----<RELEVANT>-----
    QComboBox *myComboBox;
    QLabel *myLabel;
public slots:
    void mySlot(int idx);
    //----</RELEVANT>----

};
#endif // MAINWINDOW_H
{% endhighlight %}

{% highlight c++ %}
#include "mainwindow.h"

MainWindow::MainWindow(QWidget *parent)
    : QDialog(parent)
{
    myGrid = new QGridLayout(this);
    myLabel = new QLabel("-");
    //<RELEVANT>
    myComboBox = new QComboBox();
        //we fill myComboBox with some stuff:
    myComboBox->addItem("AAA");
    myComboBox->addItem("BBB");
    myComboBox->addItem("CCC");
    myComboBox->addItem("DDD");
        //and we connect the signal to the appropiate slot:
    QObject::connect (myComboBox, SIGNAL(activated(int)), this, SLOT(mySlot(int)));
    //</RELEVANT>
    myGrid->addWidget(myComboBox, 0, 0, Qt::AlignLeft);
    myGrid->addWidget(myLabel, 1, 0, Qt::AlignLeft);
}

//The slot that will read our input and do something with it:
void MainWindow::mySlot (int idx)
{
    myLabel->setText(myComboBox->itemText(idx));
}
{% endhighlight %}

And now let’s take a look at how this simple example looks:

![Default view](https://i.imgur.com/aEgo1.png)
![Dropped menu](https://i.imgur.com/SS4Yx.png)
![2nd option selected](https://i.imgur.com/S37Hx.png)

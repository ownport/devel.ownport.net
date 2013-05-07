Title: Визуализация данных с помощью graphviz
Author: d.rey
Date: 2012-02-07 07:20:00
Slug: graphviz
Tags: data,process,visualization,graphviz,flow

В многих случаях информация намного лучше воспринимается, когда она представлена графически. Для случаев построения сложных схем, графическое представление информации - необходимое условие. Кто-то для этого использует PowerPoint, кто-то Visio, кто-то Dia. Не очень удобным является подход поддержки в актуальном виде схем, созданных с помощью этих приложений - требуется постоянное вовлечение человека. И как часто это бывает схемы или диаграммы часто не отображают реальную картину состояния дел. Graphviz позволяет решить некоторые из этих вопросов.

[Graphviz](http://www.graphviz.org/) - это открытое программное обеспечения для построение графов. Принцип работы достаточно прост: подготавливается описание графа в текстовом виде, далее с помощью утилит graphviz граф может быть преобразован в различные форматы от текстового представления до PDF документов. В основном работа выполняется в командной строке, но на сайте, в разделе [Ресурсы](http://www.graphviz.org/Resources.php) можно найти много GUI приложений как платных так и бесплатных. Там же можно найти примеры использования graphviz в различных отраслях и сферах: инжиниринг, био-информатика, описание интерфейсов, программы искусственного интеллекта.

Пример простого графа:

    :::
    digraph g {
        "process1" -> "process2" -> "process3";
    }

![](http://4.bp.blogspot.com/-st9Bw-8AkxU/TzCxiRecQfI/AAAAAAAAAdw/KHGTtRLgC6g/s1600/graphviz-example1.png)

С помощью ключевых слов node и edge можно управлять глобальными параметрами формой фигур, цветом, шрифтами, формами стрелок. Например:

    :::
    digraph g {
        rankdir=LR;
        node [fontsize=8,fontname="Verdana",style="rounded",];
        "input1" [shape="plaintext",label="input"];
        "output1" [shape="plaintext",label="ouput"];
        "process1" [shape="rectangle",label="Process 1"];
        "process2" [shape="rectangle",label="Process 2"];
        "input1" -> "process1";
        "process1" -> "process2";
        "process2" -> "output1";
    }
    
![](http://3.bp.blogspot.com/-0pQiNl_eTBs/TzCx_iKGa2I/AAAAAAAAAd8/0dJ00u1u_Ak/s1600/graphviz-example2.png)

Атрибут rankdir  позволяет изменить направление графа в LR (left-to-right). “node” определяет общие для всех нод параметры. Строка

    :::
    "input1" [shape="plaintext",label="input"];
    
определяет входные данные: “input1” - [идентификатор ноды](http://www.graphviz.org/content/attrs#did), [shape](http://www.graphviz.org/content/node-shapes) определяет какая форма будет использована для отображения, label http://www.graphviz.org/content/attrs#dlabel указывает на информацию, отображаемую в теле ноды. Для связки нод между собой используется “->” 

В версиях graphviz, начиная с середины ноября 2003 года текстовые метки label поддерживают HTML подобные теги. Это позволяет строить более информативные графы.

    :::
    digraph g {
        rankdir=TB;
        node [fontsize=8,fontname="Verdana",shape="rectangle",style="rounded",];
        edge [fontsize=8,fontname="Verdana",color="grey20",];
        "input1" [penwidth=0.1,label=<<table border="0"><tr><td bgcolor="grey" align="center"><font color="white">Input</font></td></tr><tr><td>path</td></tr></table>>];
        "treewalker" [label=<<table border="0"><tr><td bgcolor="grey18" align="center"><font color="white">treewalker</font></td></tr><tr><td>core3.processes.treewalker3</td></tr><tr><td>Scan files and directories</td></tr></table>>];
        "filehash" [label=<<table border="0"><tr><td bgcolor="grey18" align="center"><font color="white">filehash</font></td></tr><tr><td>core3.processes.filehash2</td></tr><tr><td>Calculate MD5 hash sum</td></tr></table>>];
        "printer" [label=<<table border="0"><tr><td bgcolor="grey18" align="center"><font color="white">printer</font></td></tr><tr><td>__main__.printer</td></tr><tr><td>Print out result on the screen</td></tr></table>>];
    
        "treewalker-filehash" [penwidth=0.1,label=<<table border="0"><tr><td bgcolor="grey" align="center"><font color="white">packet</font></td></tr><tr><td>path</td></tr></table>>];
        "filehash-printer" [penwidth=0.1,label=<<table border="0"><tr><td bgcolor="grey" align="center"><font color="white">packet</font></td></tr><tr><td>path</td></tr><tr><td>md5hashsum</td></tr></table>>];
    
        "input1" -> "treewalker" -> "treewalker-filehash" -> "filehash";
        "filehash" -> "filehash-printer" -> "printer";
    }
    
![](http://2.bp.blogspot.com/-6Av6mtZjH5s/TzCy96IouSI/AAAAAAAAAeI/gLCOfHZI7JU/s1600/graphviz-example3.png)

Детальное описание по работе с graphviz можно найти по ссылкам ниже

- Graphviz - Graph Visualization Software [http://www.graphviz.org](http://www.graphviz.org/)
- Graphviz Node Shapes [http://www.graphviz.org/content/node-shapes](http://www.graphviz.org/content/node-shapes)
- Graphviz Color Names [http://www.graphviz.org/doc/info/colors.html](http://www.graphviz.org/doc/info/colors.html)
- Описание Graphviz на lib.custis.ru [http://lib.custis.ru/Graphviz](http://lib.custis.ru/Graphviz)


---
layout: post
title:  "table好帮手DataTables"
date:   2017-10-30
categories: web
tags: datatables
excerpt: 一款优秀的table扩展，可以对表格数据进行搜索、排序、分页等。
---

> 说明：本文翻译自 [DataTables官方文档](https://www.datatables.net)，译者：yikai.shao


DataTables有两种处理数据的模式（排序，搜索，分页等）：

  - 客户端处理 - 所有的数据在前端已经加载，由浏览器进行处理。
  - 服务端处理 - 表格的重绘通过ajax请求进行，数据在服务端进行加工处理。


这两种有个自的有点和缺点，但是究竟用哪一种主要取决于表格有多少行数据。粗略来说，如果数据 `< 10,000` 行就用客户端模式，如果数据量`> 100,000` 行就用服务端来处理。数据量在这两个区间之外的，选择哪一种取决于你的app内在逻辑以及你想怎么呈现数据。

需要注意的是，这两种模式不能混合来使用。

------

### Data source types

##### Arrays

```javascript
var data = [
    [
        "Tiger Nixon",
        "System Architect",
        "Edinburgh",
        "5421",
        "2011/04/25",
        "$3,120"
    ],
    [
        "Garrett Winters",
        "Director",
        "Edinburgh",
        "8422",
        "2011/07/25",
        "$5,300"
    ]
]

// 使用方法
$('#example').DataTable( {
    data: data
} );




```

效果

![Alt text](/image/2017/datatables_01.png "arrays data source")


##### Objects

```javascript
[
    {
        "name":       "Tiger Nixon",
        "position":   "System Architect",
        "salary":     "$3,120",
        "start_date": "2011/04/25",
        "office":     "Edinburgh",
        "extn":       "5421"
    },
    {
        "name":       "Garrett Winters",
        "position":   "Director",
        "salary":     "$5,300",
        "start_date": "2011/07/25",
        "office":     "Edinburgh",
        "extn":       "8422"
    }
]

// 使用方法
$('#example').DataTable( {
    data: data,
    columns: [
        { data: 'name' },
        { data: 'position' },
        { data: 'salary' },
        { data: 'office' }
    ]
} );
```
![Alt text](/image/2017/datatables_02.png "arrays data source")


##### Instances

```javascript
function Employee ( name, position, salary, office ) {
    this.name = name;
    this.position = position;
    this.salary = salary;
    this._office = office;
 
    this.office = function () {
        return this._office;
    }
};
 

// 使用方法
$('#example').DataTable( {
    data: [
        new Employee( "Tiger Nixon", "System Architect", "$3,120", "Edinburgh" ),
        new Employee( "Garrett Winters", "Director", "$5,300", "Edinburgh" )
    ],
    columns: [
        { data: 'name' },
        { data: 'salary' },
        { data: 'office' },
        { data: 'position' }
    ]
} );
```
![Alt text](/image/2017/datatables_03.png "arrays data source")



### Data sources

有以下三种数据来源

  - DOM (i.e. the plain HTML of the document)
  - Javascript
  - Ajax sourced data

##### DOM
这是最简单的用法。DataTables运行时，会自动检查table中的数据信息（注意如果你通过data或ajax传递了数据，table中原来的数据会被移除）。

如果使用DOM作为数据源，DataTables会默认使用arrays，但是你仍旧可以使用 `columns.data` 选项来初始化数据。

##### HTML5

假如table已经存在，DataTables支持 `data-*` 属性，它会自动检测html单元格中的这些属性：

  - data-sort 和 data-order - 数据排序
  - data-filter 或 data-search - 数据搜索

```html
<tr>
    <td data-search="Tiger Nixon">T. Nixon</td>
    <td>System Architect</td>
    <td>Edinburgh</td>
    <td>61</td>
    <td data-order="1303682400">Mon 25th Apr 11</td>
    <td data-order="3120">$3,120/m</td>
</tr>
```

记住HTML5所有单元格 `data-*` 属性必须一致，否则会出现异常。

##### Javascript

你也可以使用 `data` 参数来初始化数据，array、object或object格式都行！只要JavaScript能读取到这些数据，你可以将这些数据发送给DataTables（来自于ajax，websocket或者仅仅是一个array data）

这个方法配合DataTables API特别有用，尤其是 `row.add()` 和 `row.remove()` 方法可以用来动态增加或移除表格数据，当然哪种数据来源都行。

##### Ajax

Ajax 数据源很像 JavaScript 数据源，DataTables会通过ajax请求来获取数据。这种方式有助于将数据获取与从数据展示页面分离。Ajax数据源由 `ajax` 参数控制，最简单的方法是，你设置它的属性值为一个字符串，通过URL来指向你想加载的数据源。

像Javascript的数据源一样，Ajax数据源也可以是 object 或 arrays 格式，但是，instances不行（因为它不能用JSON格式表示）。

服务端处理由于每个页面的DataTables数据都是由ajax请求获取的，所以这种方式允许你使用数据库引擎来处理大量数据的情况。



###### 加载数据

```javascript
$('#myTable').DataTable( {
    ajax: '/api/myData'
} );
```

###### JSON 数据源

1) 简单的array：
```javascript
[
    {
        "name": "Tiger Nixon",
        "position": "System Architect",
        "salary": "$320,800",
        "start_date": "2011/04/25",
        "office": "Edinburgh",
        "extn": "5421"
    },
    ...
}

// 上面的json格式可以直接使用
$('#myTable').DataTable( {
    ajax: {
        url: '/api/myData',
        dataSrc: ''
    },
    columns: [ ... ]
} );
```
2) 带有`data`属性的Object —— 注意，由于DataTables默认会去数据源中查找`data`属性。
```javascript
{
    "data": [
        {
            "name": "Tiger Nixon",
            "position": "System Architect",
            "salary": "$320,800",
            "start_date": "2011/04/25",
            "office": "Edinburgh",
            "extn": "5421"
        },
        ...
    ]
}

// 这里的dataSrc可以省略
$('#myTable').DataTable( {
    ajax: '/api/myData',
    columns: [ ... ]
} );
 
// 或者！
 
$('#myTable').DataTable( {
    ajax: {
        url: '/api/myData',
        dataSrc: 'data' // 这里为'data'！
    },
    columns: [ ... ]
} );
```

3) 带有`staff`属性的Object：
```javascript
{
    "staff": [
        {
            "name": "Tiger Nixon",
            "position": "System Architect",
            "salary": "$320,800",
            "start_date": "2011/04/25",
            "office": "Edinburgh",
            "extn": "5421"
        },
        ...
    ]
}

$('#myTable').DataTable( {
    ajax: {
        url: '/api/myData',
        dataSrc: 'staff' // 这里为'staff'！
    },
    columns: [ ... ]
} );
```

###### Column data points

现在我们知道了DataTables如何获取数据，但我们仍需要知道每一行如何获取数据 —— 这是通过 `columns.data` 选项实现的。

1) array数据：

```javascript
[
    "Tiger Nixon",
    "System Architect",
    "$320,800",
    "2011/04/25",
    "Edinburgh",
    "5421"
]

// columns可以省略
$('#myTable').DataTable( {
    ajax: ...
} );
 
// 或者！
 
$('#myTable').DataTable( {
    ajax: ...,
    columns: [
        { data: 0 },
        { data: 1 },
        { data: 2 },
        { data: 3 },
        { data: 4 },
        { data: 5 }
    ]
} );
```

2) object数据：

```javascript
{
    "name": "Tiger Nixon",
    "position": "System Architect",
    "salary": "$320,800",
    "start_date": "2011/04/25",
    "office": "Edinburgh",
    "extn": "5421"
}

$('#myTable').DataTable( {
    ajax: ...,
    columns: [
        { data: 'name' },
        { data: 'position' },
        { data: 'salary' },
        { data: 'state_date' },
        { data: 'office' },
        { data: 'extn' }
    ]
} );
```

3) 嵌套objects：

```javascript
{
    "name": "Tiger Nixon",
    "hr": {
        "position": "System Architect",
        "salary": "$320,800",
        "start_date": "2011/04/25"
    },
    "contact": {
        "office": "Edinburgh",
        "extn": "5421"
    }
}

$('#myTable').DataTable( {
    ajax: ...,
    columns: [
        { data: 'name' },
        { data: 'hr.position' },
        { data: 'hr.salary' },
        { data: 'hr.state_date' },
        { data: 'contact.office' },
        { data: 'contact.extn' }
    ]
} );
```

##### Setting options

示例：
```javascript
$('#example').DataTable( {
    paging: false,
    scrollY: 400
} );
```

##### Common options

  - `ajax` - Ajax data source configuration
  - `data` - Javascript sourced data
  - `serverSide` - Enable server-side processing
  - `columns.data` - Data source options for a column
  - `scrollX` - Horizontal scrolling
  - `scrollY` - Vertical scrolling

##### Setting defaults

一个项目中可能多个地方使用到DataTables，这时能对DataTables进行统一设置就好了。我们可以用 `$.fn.dataTable.defaults` 进行设置。

在以下示例中，我们先统一设置搜索和排序为`false`，然后在实例化table的时候，再根据需要将其`ordering`设置为`true`。
```javascript
// Disable search and ordering by default
$.extend( $.fn.dataTable.defaults, {
    searching: false,
    ordering:  false
} );
 
// For this specific table we are going to enable ordering
// (searching is still disabled)
$('#example').DataTable( {
    ordering: true
} );
```

##### Extensions
DataTables有很多扩展，进行相应的配置即可。配置需要的属性取决于这个扩展的需要，这些扩展必须按照一定的顺序来加载。

例如，select扩展，可以通过将`select`属性设置为`true`来激活。

```javascript
$('#myTable').DataTable( {
    select: true
} );
```

##### 多语言设置

```javascript
$('#example').DataTable( {
    language: {
        search: "Search in table:"
    }
} );

// 也可以！

$('#example').DataTable( {
    language: {
        processing:     "Traitement en cours...",
        search:         "Rechercher&nbsp;:",
        lengthMenu:    "Afficher _MENU_ &eacute;l&eacute;ments",
        info:           "Affichage de l'&eacute;lement _START_ &agrave; _END_ sur _TOTAL_ &eacute;l&eacute;ments",
        infoEmpty:      "Affichage de l'&eacute;lement 0 &agrave; 0 sur 0 &eacute;l&eacute;ments",
        infoFiltered:   "(filtr&eacute; de _MAX_ &eacute;l&eacute;ments au total)",
        infoPostFix:    "",
        loadingRecords: "Chargement en cours...",
        zeroRecords:    "Aucun &eacute;l&eacute;ment &agrave; afficher",
        emptyTable:     "Aucune donnée disponible dans le tableau",
        paginate: {
            first:      "Premier",
            previous:   "Pr&eacute;c&eacute;dent",
            next:       "Suivant",
            last:       "Dernier"
        },
        aria: {
            sortAscending:  ": activer pour trier la colonne par ordre croissant",
            sortDescending: ": activer pour trier la colonne par ordre décroissant"
        }
    }
} );
```

Ajax 加载配置json

```javascript
$('#example').DataTable( {
    language: {
        url: '/localisation/fr_FR.json'
    }
} );
```

#### 服务端处理

从DOM读取数据比较慢而且笨拙，尤其是面对巨量的数据时。通过服务端处理，可以将这个“重活”交给服务端的数据库引擎来处理，这样你就可以轻松应对百万级别的数据了。

使用服务端处理的时候，DataTables在页面上渲染信息的时候都会发送ajax请求（例如分页，排序，搜索等）。DataTables会发送变量到服务端，然后取回DataTables需要的响应格式的数据。

服务端处理可以通过`serverSide`配置项来激活，服务端处理路径可以用`ajax`配置。

##### Sent parameters

使用服务端处理的时候，DataTables需要发送以下数据给服务端，以便让服务端知道需要什么样的数据。

![Alt text](/image/2017/datatables_04.png "sent parameters")
![Alt text](/image/2017/datatables_05.png "sent parameters")

```javascript
{
    "draw": 1,
    "recordsTotal": 57,
    "recordsFiltered": 57,
    "data": [
        [
            "Angelica",
            "Ramos",
            "System Architect",
            "London",
            "9th Oct 09",
            "$2,875"
        ],
        [
            "Ashton",
            "Cox",
            "Technical Author",
            "San Francisco",
            "12th Jan 09",
            "$4,800"
        ],
        ...
    ]
}
```
##### Returned data

一旦DataTables带着如上参数去服务端请求数据，服务端会返回以下属性的json数据。
![Alt text](/image/2017/datatables_06.png "sent parameters")

以上参数可以控制整个table，以下参数可以作用于单独的行：

![Alt text](/image/2017/datatables_07.png "sent parameters")

```javascript
{
    "draw": 1,
    "recordsTotal": 57,
    "recordsFiltered": 57,
    "data": [
        {
            "DT_RowId": "row_3",
            "DT_RowData": {
                "pkey": 3
            },
            "first_name": "Angelica",
            "last_name": "Ramos",
            "position": "System Architect",
            "office": "London",
            "start_date": "9th Oct 09",
            "salary": "$2,875"
        },
        {
            "DT_RowId": "row_17",
            "DT_RowData": {
                "pkey": 17
            },
            "first_name": "Ashton",
            "last_name": "Cox",
            "position": "Technical Author",
            "office": "San Francisco",
            "start_date": "12th Jan 09",
            "salary": "$4,800"
        },
        ...
    ]
}
```

配置示例：

```javascript
$('#example').DataTable( {
    serverSide: true,
    ajax: '/data-source'
} );

// 或者！
$('#example').DataTable( {
    serverSide: true,
    ajax: {
        url: '/data-source',
        type: 'POST'
    }
} ); 
```

[完整示例](https://www.datatables.net/examples/server_side/simple.html)

![Alt text](/image/2017/datatables_08.png "sever-side processing")
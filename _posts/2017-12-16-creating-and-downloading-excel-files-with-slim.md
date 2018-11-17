---
title: Creating and downloading Excel files with Slim
date: 2017-12-16
layout: post
comments: true
published: true
description: 
keywords: 
---

Maybe you need the possibility to create excel files and automaticly download it.

To be able to create Excel docs I'm using [PhpSpreadsheet](https://github.com/PHPOffice/PhpSpreadsheet). 

Here is a tiny example how to create and download the created file directly from your server.

## Installation

```bash
composer require phpoffice/phpspreadsheet
```

## Controller action

Add a new route and a controller action with this code:

File: config/routes.php

```php
$app->get('/excel', 'App\Controller\ExcelController:downloadExcelAction');
```

```php
<?php

namespace App\Controller;

use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

class UserController
{
    public function downloadExcelAction(Request $request, Response $response): ResponseInterface
    {
        $excel = new Spreadsheet();

        $sheet = $excel->setActiveSheetIndex(0);
        $cell = $sheet->getCell('A1');
        $cell->setValue('Test');
        $sheet->setSelectedCells('A1');
        $excel->setActiveSheetIndex(0);

        $excelWriter = new Xlsx($excel);

        $excelFileName = __DIR__ . '/file.xlsx';
        $excelWriter->save($excelFileName);

        // For Excel2007 and above .xlsx files   
        $response = $response->withHeader('Content-Type', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
        $response = $response->withHeader('Content-Disposition', 'attachment; filename="file.xlsx"');

        $stream = fopen($excelFileName, 'r+');
        
        return $response->withBody(new \Slim\Http\Stream($stream));
    }
}
```

Then open the url: http://localhost/project/excel and the download should start automatically.

## Downloading CSV files

Creating an CSV file is even simpler.

Create a new route:

```php
$app->get('/csv', 'App\Controller\ExcelController:downloadCsvAction');
```

Create a new action:

```php
public function downloadCsvAction(Request $request, Response $response): ResponseInterface
{
    $list = array(
        array('aaa', 'bbb', 'ccc', 'dddd'),
        array('123', '456', '789'),
        array('"aaa"', '"bbb"')
    );

    $stream = fopen('php://memory', 'w+');

    foreach ($list as $fields) {
        fputcsv($stream, $fields, ';');
    }
    
    rewind($stream);

    $response = $response->withHeader('Content-Type', 'text/csv');
    $response = $response->withHeader('Content-Disposition', 'attachment; filename="file.csv"');

    return $response->withBody(new \Slim\Http\Stream($stream));
}
```
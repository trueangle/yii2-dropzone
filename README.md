Yii2 Dropzone
=============
DropzoneJs Extention for Yii2

A port of [DropzoneJs](http://www.dropzonejs.com/) for Yii2 Framework

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist perminder-klair/yii2-dropzone "dev-master"
```

or add

```
"perminder-klair/yii2-dropzone": "dev-master"
```

to the require section of your `composer.json` file.


Usage
-----

Once the extension is installed, simply use it in your code by to create Ajax upload area :

```php
echo \kato\DropZone::widget();
```


To pass options : (More details at [dropzonejs official docs](http://www.dropzonejs.com/#toc_6) )

```php
echo \kato\DropZone::widget([
       'options' => [
           'maxFilesize' => '2',
       ],
       'clientEvents' => [
           'complete' => "function(file){console.log(file)}",
           'removedfile' => "function(file){alert(file.name + ' is removed')}"
       ],
   ]);
```

Example of upload method :

```php
public function actionUpload()
{
    $fileName = 'file';
    $uploadPath = './files';

    if (isset($_FILES[$fileName])) {
        $file = \yii\web\UploadedFile::getInstanceByName($fileName);

        //Print file data
        //print_r($file);

        if ($file->saveAs($uploadPath . '/' . $file->name)) {
            //Now save file data to database

            echo \yii\helpers\Json::encode($file);
        }
    }

    return false;
}
```
An example of an AJAX request to get a list of the downloaded files with the ability to remove them
```javascript
   function deleteFile(file) {
            var name;
            if (file.id) {
                name = file.id;
            } else {
                var response = JSON.parse(file.xhr.response);
                name = response.name;
            }
            $.ajax({
                type: 'POST',
                url: 'URL_DELETE_ACTION',
                data: {name: name},
                dataType: 'html',
                success: function (response) {
                    if (!response) {
                        console.log("Deleting error");
                    }
                }
            });

   }

  function getFiles(dropzone) {
            $.ajax({
                type: 'POST',
                url: 'URL_GET_FILES_ACTION',
                dataType: 'json',
                success: function (response) {
                    if (response) {
                        for (var i in response) {
                            dropzone.emit('addedfile', response[i]);
                            dropzone.emit('thumbnail', response[i], response[i].url);
                            dropzone.files.push(response[i]);
                        }
                    }
                }
            });
        }
```
```php
    echo \kato\DropZone::widget([
        'options' => [
            'url' => \Yii::$app->getUrlManager()->createUrl(['doctor/upload', 'id' => $model->id]),
            'addRemoveLinks' => true,

        ],
        'clientEvents' => [
            'sending' => "function(file, xhr, formData) {
                console.log(file);
                }",

            'removedfile' => "function(file) {deleteFile(file);}",//deleteFile func call


        ], 'files' => 'getFiles(this)',//getFiles func call

    ]);
```

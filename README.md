# Fork Differences

This fork fixes the function node_publish. You can use this function to generate private download links for files stored on your MEGA account. node_public_handle was also created to assist with this process, though it was not fully documented when it was written a year ago.

Here is how I implemented the function node_publish:

```php
//Filename you want publish a link for.
$filename = "Test.zip";

//Returns the full list of files.
$files = $mega->node_list();

//This loop is not efficient done for testing.
//Find the file in the array and return its position.
$x = 0;
while ($x < count($files['f'])) {
	if (!empty($files['f'][$x]['a']['n'])) {
		if ($files['f'][$x]['a']['n'] == $filename) {
			echo "Found: ".$x;
			$foundnumber = $x;
		}
	}
	$x++;
}

//Get the publish link, and display.
echo "<pre>";
echo "URL: ";
if (!empty($foundnumber)) {
	$url = $mega->node_publish($files, $foundnumber);
	echo $url;
}
echo "</pre>";
```


MEGA PHP Client Library
=======================

PHP client library for the [MEGA API](https://mega.co.nz/#developers).

*Note: This library is still under development and incomplete, so the API is subject to change.*

Requirements
------------

* PHP 5.x
* PHP Mcrypt extension
* PHP OpenSSL extension
* PHP cURL extension

Creating the client
-------------------

### Using the constructor

```php
$mega = new MEGA();
```

### Using a static factory method

```php
$mega = MEGA::create_from_login($email, $password);
```

This is equivalent to:

```php
$mega = new MEGA();
$mega->user_login_session($email, $password);
```

### Changing the default MEGA API server

```php
MEGA::set_default_server(MEGA::SERVER_EUROPE);
```

Working with public files
-------------------------

Download public files not require authentication.

### Gettings file info

```php
$mega = new MEGA();

$file_info = $mega->public_file_info($ph, $key);
var_dump($file_info);

// Print filename and size
echo 'Filename: ' . $file_info['at']['n'];
echo 'Size: ' . $file_info['s'];
```

### Using links

```php
$file_info = $mega->public_file_info_from_link($link);
```

This is equivalents to:

```php
$info = MEGA::parse_link($link);
$file_info = $mega->public_file_info($info['ph'], $info['key']);
```

### Downloading public files

```php
// Save file to current directory.
$filepath = $mega->public_file_save($ph, $key);
echo 'File saved in ' . $filepath;

// Equivalent using exported link
$filepath = $mega->public_file_save_from_link($link);
echo 'File saved in ' . $filepath;
```

See below for more examples.

Downloading files
-----------------

### Using streams

```php
// Write to file
$fp = fopen($file, 'wb');
$size = $mega->public_file_download($ph, $key, $fp);
fclose($fp);
```

### Returning content

```php
// Get content using temporary stream
$content = $mega->public_file_download($ph, $key);
```

### Saving to disk

```php
// Save file to temporary directory.
$tmp = sys_get_temp_dir();
$file = $mega->public_file_save($ph, $key, $tmp);
echo 'File saved in ' . $file;
```

Private files
-------------

### Listing

```php
$mega = MEGA::create_from_user($email, $password);

$files = $mega->node_list();
print_r($files);

// Get file info
$file_info = $mega->node_file_info($files['f'][5]);
print_r($file_info);
```

### Downloading

* The ```node_file_save()``` function is equivalent to ```public_file_save()```
* The ```node_file_download()``` function is equivalent to ```public_file_download()```

User session
------------

### Saving session

```php
$mega = MEGA::create_from_user($email, $password);

// ...

// Get current session as a base64 string
$session = MEGA::session_save($mega);

// Store in a safe place!
db_store_session($session);
```

### Restoring session

```php
// Retrive saved session
$session = db_get_session();

// Create client from previous session
$mega = MEGA::create_from_session($session);

// ...
```

Status
------

### Operations that don't require user authentication

| Method | Description | Status |
| ------ | ----------- | ------ |
| `public_file_info` | Request public file info | Implemented |
| `public_file_info_from_link` | Request public file info | Implemented |
| `public_file_download` | Download a public file | Implemented |
| `public_file_download_from_link` | Download a public file | Implemented |
| `public_file_save` | Download and save a public file to disk | Implemented |
| `public_file_save_from_link` | Download and save a public file to disk | Implemented |

### Operations that require user authentication

| Method | Description | Status |
| ------ | ----------- | ------ |
| `node_list`| Retrieve folder or user nodes | Implemented |
| `node_file_info` | Request file node info | Implemented |
| `node_file_download` | Download a file node | Implemented |
| `node_file_save` | Download and save a file node to disk | Implemented |
| `node_add`| Add/copy nodes | Not implemented |
| `node_delete`| Delete node | Not implemented |
| `node_move`| Move node | Not implemented |
| `node_update`| Set node attributes | Not implemented |
| `node_publish` / `node_unpublish`| Create/delete public handle | Not implemented |
| `node_share`| Create/modify/delete outgoing share | Not implemented |

Credits
-------

* This library has been written by Sergio Martín ([@smartinm](http://twitter.com/smartinm)) as a port of official MEGA Javascript code.

* Part of the code is based on the work done by [Julien Marchand](http://julien-marchand.fr/).

* This projected is licensed under the terms of the MIT license.

[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/smartinm/mega-php-client/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

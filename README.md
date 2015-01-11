# Sistema de caché de archivos en PHP

Esta clase provee una forma fácil, orientada a objetos de trabajar con una caché basada en el sistema de archivos.

[Leer más](http://emiliocobos.net/php-cache/) | [El autor](http://emiliocobos.net) | [Contribuidores](https://github.com/ecoal95/php-cache/graphs/contributors)

## Comenzando
Lo primero que tienes que hacer es incluir el archivo
```php
require 'Cache.php';
```

### Opciones
Hay dos opciones: `cache_dir` y `expires`.

* `cache_dir` es el directorio donde se almacenará la caché. Por defecto es un directorio relativo (`cache`)
* `expires` es el tiempo *en horas* que debe de pasar para que un item de la caché expire

**Importante**: Verifica que el archivo configurado como `cache_dir` tenga permisos de escritura. Además, **si almacenas datos que no deberían ser accesibles públicamente, tu directorio `cache_dir` debería de tener un archivo `.htaccess` con la siguiente línea:**
```
deny from all
```

Puedes modificar las opciones así:

```php
Cache::configure('nombre_opción', 'valor');
```

O así:

```php
Cache::configure(array(
	'nombre_opcion_1' => 'valor 1',
	// ...
));
```
### Almacenando datos
Para almacenar *cualquier tipo de dato* en la caché usa el método `put()` pasando un identificador (key) y el valor.
```php
Cache::put('identificador', 'valor');
```

### Obteniendo datos
Para obtener datos almacenados en la caché usa:

```php
Cache::get('identificador');
```

### Borrando datos
Simplemente invoca al método `delete()` pasando la key:
```php
Cache::delete('identificador');
```

### Borrando toda la caché
Usa el método `flush()` para borrar todos los datos almacenados en la caché:
```php
Cache::flush();
```

## Avanzado
Este sistema permite pasar un segundo argumento a `get()` y `put()`, que dice si los datos se van a guardar en forma de string directamente, o no. Podría ser útil para cachés de páginas enteras:

```php
if( !($cached_homepage = Cache::get('index_cached', true)) ) {
	ob_start();
	// Generar la página principal dinámica
	// ...
	$cached_homepage = ob_get_contents();
	Cache::put('index_cached', $cached_homepage);
}
echo $cached_homepage;
```


## Velocidad
Este sistema de caché está basado en el acceso a archivos, así que es más lento que memcached o apc. No obstante, **cuanto mayores son los datos a almacenar, menor es la diferencia de velocidad**. Puedes comprobarlo ejecutando el archivo `test.php` del repositorio. Los resultados en un servidor local frente a `apc`:

**Volumen de datos pequeño** (nótese que _datos pequeños_ son una pequeña string, así que en estos casos no merece la pena acceder al sistema de ficheros). Aún así la diferencia real de tiempo no es muy grande.
<pre>Diferencia total: 0.11318588256836 segundos
Diferencia porcentual de velocidad: 48.071951581333%</pre>

**Volumen de datos medio**
<pre>Diferencia total: 0.099838733673096 segundos
Diferencia porcentual de velocidad: 7.1366305302393%</pre>

**Volumen de datos grande** (con datos grandes la **diferencia de velocidad es casi nula**)
<pre>Diferencia total: 0.29143214225769 segundos
Diferencia porcentual de velocidad: 0.45639094202677%</pre>

## Concurrencia
Ahora la librería es resistente a problemas de concurrencia.
Ver [pull request](https://github.com/ecoal95/php-cache/pull/3) de [@logicaalternativa](https://github.com/logicaalternativa).

## Tests
[@logicaalternativa](https://github.com/logicaalternativa) añadió tests de rendimiento más rigurosos que mi archivo `test.php`, y tests de concurrencia. Para leer sus comentarios ver el archivo `tests/README.md`.

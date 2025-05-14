# Monitor de cambios en sitios web

Script de Python diseñado para monitorear cambios en el contenido de sitios web utilizados como fuente de información para confeccionar la Selección Informativa Desarrollo Inmobilaio y que no ofrecen feeds RSS. El script se ejecuta en Google Colab y utiliza Google Drive para persistir el estado (un hash del contenido) entre ejecuciones, permitiendo detectar si el contenido ha sido actualizado o modificado.

A diferencia de monitorear el HTML crudo, este script limpia el código (eliminando scripts, estilos, comentarios y otro "ruido") antes de calcular el hash, reduciendo la sensibilidad a cambios irrelevantes y enfocándose más en las modificaciones del contenido principal.

## Características

* Monitorea una lista configurable de URLs.
* Obtiene el contenido HTML de cada URL.
* Utiliza BeautifulSoup para limpiar el HTML, eliminando elementos que suelen cambiar sin afectar el contenido visible (scripts, estilos, comentarios).
* Calcula un hash (SHA256) del contenido HTML limpio.
* Guarda el hash del último estado conocido en tu Google Drive para cada URL.
* Compara el hash actual con el guardado para detectar cambios.
* Informa sobre las URLs donde se detectaron cambios imprimiendo en la salida de Colab.
* Incluye manejo básico de errores para problemas de red (HTTP, SSL).
* Ideal para chequeos manuales periódicos.

## Configuración

Modifica las siguientes variables en la primera celda del script:

* `urls_a_monitorear`: Una lista de cadenas de texto, donde cada cadena es una URL completa del sitio web a monitorear.
* `drive_base_path`: La ruta completa dentro del entorno de Colab donde se montará tu Google Drive, apuntando a la carpeta donde se guardarán los hashes de los estados. Por defecto, es `/content/drive/MyDrive/web_monitoring_hashes`.

## Manejo de Errores y Limitaciones

* **Errores 403 Forbidden:** Algunos sitios web implementan medidas agresivas anti-bot que detectan y bloquean las solicitudes de scripts automatizados (a menudo basándose en la IP del servidor de nube, el `User-Agent`, o la falta de ejecución de JavaScript). Este script incluye un manejo básico con un `User-Agent` común, pero **no puede garantizar la evasión** de medidas sofisticadas. Las URLs que persistentemente den error 403 probablemente no podrán ser monitoreadas con este método simple.
* **Errores SSL:** Los errores de verificación de certificado SSL (`SSLError`) indican un problema con el certificado del sitio web mismo o con la capacidad del entorno de Colab para verificarlo. El script reportará este error. La opción de desactivar la verificación SSL (`verify=False`) existe en el código pero está comentada y **NO SE RECOMIENDA** por motivos de seguridad (riesgo de ataques Man-in-the-Middle). Si un sitio tiene errores de certificado, su confiabilidad es cuestionable.
* **Contenido Dinámico (JavaScript):** El script utiliza `requests` para obtener el HTML. Esto solo descarga el contenido inicial servido por el servidor. Si el contenido principal del sitio se carga *después* usando JavaScript, este script no detectará cambios en ese contenido cargado dinámicamente.
* **Ejecución en Colab:** Google Colab no es un servicio de hosting 24/7. Las sesiones son temporales. Este script está pensado para ejecución manual o activada externamente de forma periódica, no para monitoreo continuo.

## Autor

Juan Draghi - Biblioteca del Consejo Profesional de Arquitectura y Urbanismo

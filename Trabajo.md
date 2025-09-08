# Web Workers in JavaScript
<p align="justify">
LJavaScript es un lenguaje monohilo, lo que implica que todas sus operaciones se ejecutan en un solo hilo (el hilo principal), lo cual puede causar bloqueos en la interfaz de usuario cuando se realizan tareas intensivas. Para optimizar el rendimiento y evitar estas interrupciones, se introdujo la API de Web Workers, que permite ejecutar scripts en hilos separados. Esto permite que tareas pesadas se realicen en segundo plano sin afectar la interacción con el usuario (Simpson, 2023; Pajuelo & Verdú, 2016).
</p> 

## ¿Qué son los Web Workers?
<p align="justify">
Los Web Workers son scripts de JavaScript que se ejecutan en segundo plano, de manera independiente del hilo principal y sin acceso directo al DOM ni a las variables del contexto global. Su objetivo principal es realizar tareas computacionalmente costosas sin afectar el rendimiento de la interfaz (W3C, 2009; Isaacs & Burns, 2013).
</p>
<p align="justify">
En otras palabras, un Web Worker funciona como un “trabajador” independiente que recibe instrucciones, realiza su tarea y devuelve los resultados, mientras el hilo principal sigue atendiendo a la interfaz.
</p> 

## Tipos de Web Workers
### Dedicated Workers
<p align="justify">
>Los Dedicated Workers son aquellos que están vinculados a una única página o script. Una vez que la página se cierra, el worker también finaliza. Son ideales para tareas exclusivas de una sola aplicación y ayudan a mantener separadas las operaciones intensivas del hilo principal (Isaacs & Burns, 2013).
</p>
### Shared Worker
<p align="justify">
>Los Shared Workers permiten que múltiples contextos (pestañas, iframes o scripts) del mismo origen compartan un único worker. Esto es útil para coordenar datos o procesos comunes entre varias partes de la aplicación (similares en modelo a los Dedicated Workers, pero con alcance compartido) (Krauweel & Jongmans, 2017).
</p>
### Service Workers
<p align="justify">
>Los Service Workers son especializados: actúan como intermediarios entre la aplicación web, el navegador y la red. Funcionan en segundo plano incluso cuando la página está cerrada, gestionan el cacheado de recursos, permiten que la aplicación funcione offline y habilitan funciones como notificaciones push. Son fundamentales en el desarrollo de Progressive Web Apps (PWA) (Wikipedia, 2025; Krauweel & Jongmans, 2017).
</p>

![FUENTE: introduction to webworkers](https://images.ctfassets.net/23aumh6u8s0i/1hW4Sr1YsDa5mCXBfVaNWb/e875abf8ef8d19354b6cc6a24208a9b9/Performing-Tasks-With-and-Without-Web-Worker)

<img src="https://images.ctfassets.net/23aumh6u8s0i/1hW4Sr1YsDa5mCXBfVaNWb/e875abf8ef8d19354b6cc6a24208a9b9/Performing-Tasks-With-and-Without-Web-Worker" alt="FUENTE: introduction to webworkers" width="800">


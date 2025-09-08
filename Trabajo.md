# Web Workers in JavaScript 
LJavaScript es un lenguaje monohilo, lo que implica que todas sus operaciones se ejecutan en un solo hilo (el hilo principal), lo cual puede causar bloqueos en la interfaz de usuario cuando se realizan tareas intensivas. Para optimizar el rendimiento y evitar estas interrupciones, se introdujo la API de Web Workers, que permite ejecutar scripts en hilos separados. Esto permite que tareas pesadas se realicen en segundo plano sin afectar la interacción con el usuario (Simpson, 2023; Pajuelo & Verdú, 2016).

## ¿Qué son los Web Workers?
Los Web Workers son scripts de JavaScript que se ejecutan en segundo plano, de manera independiente del hilo principal y sin acceso directo al DOM ni a las variables del contexto global. Su objetivo principal es realizar tareas computacionalmente costosas sin afectar el rendimiento de la interfaz (W3C, 2009; Isaacs & Burns, 2013).

En otras palabras, un Web Worker funciona como un “trabajador” independiente que recibe instrucciones, realiza su tarea y devuelve los resultados, mientras el hilo principal sigue atendiendo a la interfaz.

**Externo (external)**
   

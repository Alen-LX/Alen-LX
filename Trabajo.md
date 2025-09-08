## WEB WORKERS EN JAVASCRIPT <img src="https://www.desarrollolibre.net/images/example///javascript/web_workers.png" width="50" align="right">

LJavaScript es un lenguaje monohilo, lo que implica que todas sus operaciones se ejecutan en un solo hilo (el hilo principal), lo cual puede causar bloqueos en la interfaz de usuario cuando se realizan tareas intensivas. Para optimizar el rendimiento y evitar estas interrupciones, se introdujo la API de Web Workers, que permite ejecutar scripts en hilos separados. Esto permite que tareas pesadas se realicen en segundo plano sin afectar la interacción con el usuario (Simpson, 2023; Pajuelo & Verdú, 2016).
</p> 

## 1.¿Qué son los Web Workers?
<p align="justify">
Los Web Workers son scripts de JavaScript que se ejecutan en segundo plano, de manera independiente del hilo principal y sin acceso directo al DOM ni a las variables del contexto global. Su objetivo principal es realizar tareas computacionalmente costosas sin afectar el rendimiento de la interfaz (W3C, 2009; Isaacs & Burns, 2013).
</p>
<p align="justify">
En otras palabras, un Web Worker funciona como un “trabajador” independiente que recibe instrucciones, realiza su tarea y devuelve los resultados, mientras el hilo principal sigue atendiendo a la interfaz.
</p> 

## 2.Tipos de Web Workers  
### 2.1.Dedicated Workers
><div align="justify">
>Los Dedicated Workers son aquellos que están vinculados a una única página o script. Una vez que la página se cierra, el worker también finaliza. Son ideales       para tareas exclusivas de una sola aplicación y ayudan a mantener separadas las operaciones intensivas del hilo principal (Isaacs & Burns, 2013).
></div>  

### 2.2.Shared Worker
><div align="justify">
>Los Shared Workers permiten que múltiples contextos (pestañas, iframes o scripts) del mismo origen compartan un único worker. Esto es útil para coordenar datos o procesos comunes entre varias partes de la aplicación (similares en modelo a los Dedicated Workers, pero con alcance compartido) (Krauweel & Jongmans, 2017).
></div>  

### 2.3.Service Workers
><div align="justify">
>Los Service Workers son especializados: actúan como intermediarios entre la aplicación web, el navegador y la red. Funcionan en segundo plano incluso cuando la página está cerrada, gestionan el cacheado de recursos, permiten que la aplicación funcione offline y habilitan funciones como notificaciones push. Son fundamentales en el desarrollo de Progressive Web Apps (PWA) (Wikipedia, 2025; Krauweel & Jongmans, 2017).
></div> 

<div style="width:600px; margin:auto;">
  <img src="https://images.ctfassets.net/23aumh6u8s0i/1hW4Sr1YsDa5mCXBfVaNWb/e875abf8ef8d19354b6cc6a24208a9b9/Performing-Tasks-With-and-Without-Web-Worker" 
       alt="FUENTE: introduction to webworkers" width="550"><br>
  <span style="font-size:12px; display:block; text-align:left;">
    Fuente: Introduction to Web Workers
  </span>
</div>

## 3.Características principales de los Web Workers
- **Ejecución en paralelo:**  Funcionan en hilos independientes, lo que permite procesar tareas largas sin congelar la interfaz (Simpson, 2023).
  
- **Comunicación mediante mensajes:** La interacción con el hilo principal se realiza mediante postMessage y onmessage, lo cual es totalmente asincrónico (W3C, 2009).
- **Entorno aislado:** No comparten el contexto global (como window o document) con el hilo principal (Isaacs & Burns, 2013).
  
- **Acceso a APIs específicas:** Aunque no tienen acceso al DOM, pueden consultar recursos como fetch, XMLHttpRequest, IndexedDB y temporizadores (setTimeout, setInterval) (MDN, 2025).
  
- **Política de seguridad (Same-Origin Policy):** Sólo pueden ejecutar scripts del mismo origen, para evitar riesgos de seguridad (W3C, 2009).
## 4.Beneficios de usar Web Workers 
- **Mejor rendimiento general:** Permiten aprovechar múltiples núcleos del procesador y evitar el bloqueo del hilo principal (Pajuelo & Verdú, 2016).
- **Experiencia de usuario fluida:** Mantienen la interfaz responsiva mientras se ejecutan procesos pesados (Simpson, 2023).
- **Eficiencia en procesamiento intensivo:** Son ideales para tareas como renderizado gráfico, análisis de datos o cálculos complejos (Simpson, 2023).
- **Optimización de recursos:** Están diseñados para encontrar el balance óptimo entre desempeño y consumo de memoria (Pajuelo & Verdú, 2016).
#### main.js
~~~
  // Creamos el worker
const worker = new Worker("worker.js");

document.getElementById("btnStart").onclick = () => {
  worker.postMessage("iniciar");
  document.getElementById("status").textContent = "Procesando...";
};

worker.onmessage = (e) => {
  document.getElementById("status").textContent = "Resultado: " + e.data;
};
~~~
#### worker.js
~~~
onmessage = function(e) {
  if (e.data === "iniciar") {
    let suma = 0;
    for (let i = 0; i < 1e8; i++) {
      suma += i;
    }
    postMessage(suma);
  }
};
~~~
## 5.Limitaciones de los Web Workers

<p align="justify">
- Procesamiento de datos masivos: Ideal para análisis en el navegador en grandes volúmenes de información (Simpson, 2023).
</p>
<p align="justify">
- Manipulación de imágenes o gráficos: Aplicación de filtros, compresión o renderizado sin congelar la interfaz (Simpson, 2023).
</p> 
<p align="justify">
- Cálculos intensivos: Simulaciones, criptografía o IA básica funcionan mejor en segundo plano (Simpson, 2023).
</p>  
<p align="justify">
- Procesamiento de archivos: Lectura, compresión y descompresión sin afectar la experiencia del usuario (Simpson, 2023).
</p>    
<p align="justify">
- Aplicaciones offline (PWA): Los Service Workers permiten cacheado de recursos y activación de notificaciones push (Wikipedia, 2025).
</p> 

#### main.js
~~~
const worker = new Worker("worker2.js");

worker.onmessage = (e) => {
  // El worker manda el resultado, pero SOLO el main puede tocar el DOM
  document.getElementById("resultado").textContent = e.data;
};

worker.postMessage("Calcular");
~~~
#### worker.js
~~~
onmessage = () => {
  let total = 0;
  for (let i = 0; i < 1e7; i++) total += i;
  // Si intento hacer document.getElementById() aquí → ❌ ERROR
  postMessage("Total: " + total);
};
~~~
## 6.Casos de uso comunes
Los Web Workers se aplican en situaciones donde el procesamiento intensivo es inevitable:

- **Procesamiento de datos masivos:**  Ideal para análisis en el navegador en grandes volúmenes de información (Simpson, 2023).
  
- **Manipulación de imágenes o gráficos:** Aplicación de filtros, compresión o renderizado sin congelar la interfaz (Simpson, 2023).
  
- **Cálculos intensivos:** Simulaciones, criptografía o IA básica funcionan mejor en segundo plano (Simpson, 2023).
  
- **Procesamiento de archivos:** Lectura, compresión y descompresión sin afectar la experiencia del usuario (Simpson, 2023).

  
  

## 9\. WEB WORKERS EN JAVASCRIPT <img src="https://www.desarrollolibre.net/images/example///javascript/web_workers.png" width="60" align="right">
<p align="justify">
JavaScript es un lenguaje monohilo, lo que implica que todas sus operaciones se ejecutan en un solo hilo (el hilo principal), lo cual puede causar bloqueos en la interfaz de usuario cuando se realizan tareas intensivas. Para optimizar el rendimiento y evitar estas interrupciones, se introdujo la API de Web Workers, que permite ejecutar scripts en hilos separados. Esto permite que tareas pesadas se realicen en segundo plano sin afectar la interacción con el usuario (Simpson, 2023; Pajuelo & Verdú, 2016).
</p>  

## 9\.1 ¿Qué son los Web Workers?
<p align="justify">
Los Web Workers son scripts de JavaScript que se ejecutan en segundo plano, de manera independiente del hilo principal y sin acceso directo al DOM ni a las variables del contexto global. Su objetivo principal es realizar tareas computacionalmente costosas sin afectar el rendimiento de la interfaz (W3C, 2009; Isaacs & Burns, 2013).
</p>
<p align="justify">
En otras palabras, un Web Worker funciona como un “trabajador” independiente que recibe instrucciones, realiza su tarea y devuelve los resultados, mientras el hilo principal sigue atendiendo a la interfaz.
</p> 

## 9\.2 Tipos de Web Workers  
### 9\.2\.1 Dedicated Workers
><div align="justify">
>Los Dedicated Workers son aquellos que están vinculados a una única página o script. Una vez que la página se cierra, el worker también finaliza. Son ideales       para tareas exclusivas de una sola aplicación y ayudan a mantener separadas las operaciones intensivas del hilo principal (Isaacs & Burns, 2013).
></div>  

### 9\.2\.2 Shared Worker
><div align="justify">
>Los Shared Workers permiten que múltiples contextos (pestañas, iframes o scripts) del mismo origen compartan un único worker. Esto es útil para coordenar datos o procesos comunes entre varias partes de la aplicación (similares en modelo a los Dedicated Workers, pero con alcance compartido) (Krauweel & Jongmans, 2017).
></div>  

### 9\.2\.3 Service Workers
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

## 9\.3 Características principales de los Web Workers
- **Ejecución en paralelo:**  Funcionan en hilos independientes, lo que permite procesar tareas largas sin congelar la interfaz (Simpson, 2023).
  
- **Comunicación mediante mensajes:** La interacción con el hilo principal se realiza mediante postMessage y onmessage, lo cual es totalmente asincrónico (W3C, 2009).
- **Entorno aislado:** No comparten el contexto global (como window o document) con el hilo principal (Isaacs & Burns, 2013).
  
- **Acceso a APIs específicas:** Aunque no tienen acceso al DOM, pueden consultar recursos como fetch, XMLHttpRequest, IndexedDB y temporizadores (setTimeout, setInterval) (MDN, 2025).
  
- **Política de seguridad (Same-Origin Policy):** Sólo pueden ejecutar scripts del mismo origen, para evitar riesgos de seguridad (W3C, 2009).
## 9\.4 Beneficios de usar Web Workers 
- **Mejor rendimiento general:** Permiten aprovechar múltiples núcleos del procesador y evitar el bloqueo del hilo principal (Pajuelo & Verdú, 2016).
- **Experiencia de usuario fluida:** Mantienen la interfaz responsiva mientras se ejecutan procesos pesados (Simpson, 2023).
- **Eficiencia en procesamiento intensivo:** Son ideales para tareas como renderizado gráfico, análisis de datos o cálculos complejos (Simpson, 2023).
- **Optimización de recursos:** Están diseñados para encontrar el balance óptimo entre desempeño y consumo de memoria (Pajuelo & Verdú, 2016).
                                                        
#### main.js                                                 
```javascript
  
const worker = new Worker("worker.js");

document.getElementById("btnStart").onclick = () => {
  worker.postMessage("iniciar");
  document.getElementById("status").textContent = "Procesando...";
};

worker.onmessage = (e) => {
  document.getElementById("status").textContent = "Resultado: " + e.data;
};
```
#### worker.js
```javascript
onmessage = function(e) {
  if (e.data === "iniciar") {
    let suma = 0;
    for (let i = 0; i < 1e8; i++) {
      suma += i;
    }
    postMessage(suma);
  }
};
```
>Este ejemplo muestra cómo un Web Worker puede realizar un cálculo muy grande (suma de números hasta 1e8) sin bloquear la página. Mientras el worker procesa, el usuario puede seguir usando la interfaz y no se queda congelada. Demuestra el beneficio principal de los workers: mantener la experiencia fluida.

## 9\.5 Limitaciones de los Web Workers

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
```javascript
const worker = new Worker("worker2.js");

worker.onmessage = (e) => {
  // El worker manda el resultado, pero SOLO el main puede tocar el DOM
  document.getElementById("resultado").textContent = e.data;
};

worker.postMessage("Calcular");
```
#### worker.js
```javascript
onmessage = () => {
  let total = 0;
  for (let i = 0; i < 1e7; i++) total += i;
  // Si intento hacer document.getElementById() aquí → ❌ ERROR
  postMessage("Total: " + total);
};
```
>Aquí el worker calcula una suma, pero no puede cambiar el contenido del HTML directamente. En su lugar, envía el resultado al hilo principal, y este sí actualiza la página. Esto demuestra la limitación de los workers: no tienen acceso al DOM, solo pueden comunicarse mediante mensajes.

## 9\.6 Casos de uso comunes
Los Web Workers se aplican en situaciones donde el procesamiento intensivo es inevitable:

- **Procesamiento de datos masivos:**  Ideal para análisis en el navegador en grandes volúmenes de información (Simpson, 2023).
  
- **Manipulación de imágenes o gráficos:** Aplicación de filtros, compresión o renderizado sin congelar la interfaz (Simpson, 2023).
  
- **Cálculos intensivos:** Simulaciones, criptografía o IA básica funcionan mejor en segundo plano (Simpson, 2023).
  
- **Procesamiento de archivos:** Lectura, compresión y descompresión sin afectar la experiencia del usuario (Simpson, 2023).
<p align="center">
  <img src="https://web.dev/static/articles/workers-overview/image/diagram-showing-links-be-3f8d21126eb87.png" width="400">
</p>

## 9\.7 Diferencia entre Web Workers y el Main Thread
<p align="justify">
El Main Thread (hilo principal) es el encargado de manejar todo lo que el usuario ve y toca en la aplicación: botones, menús, animaciones y en general la interfaz gráfica. El problema es que, si en este mismo hilo se ejecutan tareas muy pesadas (como cálculos matemáticos grandes o procesar imágenes), la página puede quedarse "congelada" o lenta, ya que el navegador no logra responder a las interacciones a tiempo.
</p>
<p align="justify">
En cambio, los Web Workers funcionan como ayudantes que trabajan en segundo plano. Ellos se encargan de esas tareas complejas mientras el hilo principal se queda libre para responder al usuario. De esta manera, la aplicación sigue siendo rápida y fluida, sin pausas incómodas. En otras palabras, el Main Thread es como el recepcionista de una oficina (atiende al público), mientras que los Web Workers son empleados en otras salas que resuelven los cálculos complicados sin interrumpir la atención al cliente.
</p>

<p align="center">
  <img src="https://bitsofco.de/img/AfltGEBT6F-566.jpeg" width="400">
</p>
 
## 10\. ALTO RENDIMIENTO EN JAVASCRIPT
<p align="justify">
JavaScript es un lenguaje dinámico y flexible, ampliamente adoptado para crear aplicaciones web interactivas. Sin embargo, su naturaleza hace que sea propenso a problemas de rendimiento en entornos complejos. Por ello, comprender y aplicar técnicas avanzadas de optimización es esencial para mantener aplicaciones eficientes y escalables (Vepsäläinen et al., 2024)
</p>

## 10\.1 ¿Qué significa “alto rendimiento”?
<p align="justify">
Se refiere a lograr tiempos de carga rápidos, ejecución fluida, bajo uso de CPU/memoria y experiencia de usuario sin demoras perceptibles. Optimizar JavaScript no es solo escribir código más rápido, sino también reducir recursos utilizados y evitar cuellos de botella (Vepsäläinen et al., 2024) 
</p>

## 10\.2 Técnicas avanzadas de optimización
   
## 10\.2\.1 Eliminación de código muerto ("dead code")
<p align="justify">
Muchas páginas cargan librerías o funciones que no usan realmente en producción. Según Kupoluyi et al. (2021), aproximadamente el 70 % de las funciones en una página promedio no se ejecutan y eliminarlas puede reducir hasta un 60 % el tamaño de descarga, acelerando el tiempo de carga en un 25–30 % 
</p>

## 10\.2\.2 Optimización JIT y compilación especializada
<p align="justify">
Al trabajar con motores JavaScript modernos, técnicas como el typed object shapes y la especialización de bloques básicos permiten reducir pruebas de tipo dinámicas e incrementar la velocidad de ejecución en alrededor de un 25 % (Chevalier-Boisvert & Feeley, 2015) 
</p>

## 10\.2\.3 Descomposición de tareas largas

Dividir tareas largas en fragmentos más cortos evita bloquear el main thread. Según MDN (2025), esto mejora significativamente la fluidez de la interfaz al evitar interrupciones perceptibles durante la ejecución del código pesado 

## 10\.3 Limpieza de solicitudes y minificación

Reducir el número de archivos JavaScript y minificarlos (eliminar espacio, comentarios, variables innecesarias) disminuye la latencia de carga y el procesamiento inicial con impacto directo en rendimiento (Microsoft, 2023) 

## 10\.4 Uso de analíticas y métricas de rendimiento

Medir es el primer paso para optimizar. Herramientas como Lighthouse y las DevTools del navegador permiten detectar qué scripts tardan más en cargar, qué funciones generan repaints o delays, y qué fragmentos conviene refactorizar (Vepsäläinen et al., 2024)

## 10\.5 Beneficios del alto rendimiento

- Mejora experiencia de usuario: interacciones rápidas y sin bloqueos, especialmente en dispositivos móviles o con red lenta.

- Eficiencia de recursos: menos CPU, memoria y ancho de banda gastados.

- Ventaja competitiva: páginas más rápidas retienen mejor a los usuarios y mejoran métricas de negocio.

## 10\.6 Limitaciones y consideraciones

- **Complejidad técnica:** aplicar técnicas como eliminación de código muerto o especialización de JIT requiere herramientas avanzadas o conocimiento profundo de compiladores y análisis estático.

- **Riesgo de errores:** eliminar funciones sin pruebas exhaustivas puede romper funcionalidades invisibles.

- **Compatibilidad:** algunas optimizaciones avanzadas dependen de navegadores modernos y pueden no aplicarse en todos los entornos.
  
#### Ejemplo 1.js
```javascript
function suma(a, b) {
  return a + b;
}
function resta(a, b) {   // 👈 nunca se usa
  return a - b;
}
console.log(suma(5, 3));
```
#### Se elimina la función sin uso
```javascript
function suma(a, b) {
  return a + b;
}
console.log(suma(5, 3));
```
#### Ejemplo 2.js
```javascript
function saludo(nombre) {
  console.log("Hola, " + nombre + "!");
}
saludo("Mundo");

```
#### Minificación
```javascript
function saludo(n){console.log("Hola, "+n+"!")}saludo("Mundo");

```
## Referencia
-	Isaacs, S., & Burns, K. (2013). Web Workers. In Beginning Windows Store Application Development–HTML and JavaScript Edition. Apress. https://doi.org/10.1007/978-1-4302-5780-6_13.
-	Krauweel, M., & Jongmans, S.-S. T. Q. (2017). Simpler Coordination of JavaScript Web Workers. In J.-M. Jacquet & M. Massink (Eds.), Coordination Models and Languages (pp. 40–58). Springer, Cham. https://doi.org/10.1007/978-3-319-59746-1_3.
-	Pajuelo, M. A., & Verdú, J. (2016). Performance scalability analysis of JavaScript applications with web workers. IEEE Computer Architecture Letters, 15(2), 105–108. https://doi.org/10.1109/LCA.2015.2494585.
-	Simpson, J. (2023). Web Workers and Multithreading. In How JavaScript Works. Apress. https://doi.org/10.1007/978-1-4842-9738-4_14.
-	W3C. (2009). Web Workers W3C Working Draft 22 December 2009. World Wide Web Consortium..
-	MDN Contributors. (2025). Usando Web Workers. Mozilla Developer Network (MDN). Retrieved from MDN Web Workers documentation.
-	Kupoluyi, T., Chaqfeh, M., Varvello, M., Hashmi, W., Subramanian, L., & Zaki, Y. (2021). Muzeel: A dynamic JavaScript analyzer for dead code elimination in today's web. arXiv preprint arXiv:2106.08948. https://doi.org/10.48550/arXiv.2106.08948.
-	Vepsäläinen, J., Hellas, A., & Vuorimaa, P. (2024). Overview of web application performance optimization techniques. In A. Ojala, & R. Heikkilä (Eds.), Digital Business Platforms: Trends and Challenges (pp. 32–49). Springer. https://doi.org/10.1007/978-3-031-89621-7_3.
-	Chevalier-Boisvert, M., & Feeley, M. (2015). Extending basic block versioning with typed object shapes. arXiv preprint arXiv:1507.02437. https://arxiv.org/abs/1507.02437.
-	Microsoft. (2023). Rendimiento y patrones de JavaScript. Microsoft Docs. https://learn.microsoft.com/es-es/microsoft-edge/devtools-guide-chromium/javascript.

## WEB WORKERS EN JAVASCRIPT <img src="https://www.desarrollolibre.net/images/example///javascript/web_workers.png" width="60" align="right">
<p align="justify">
JavaScript es un lenguaje monohilo, lo que implica que todas sus operaciones se ejecutan en un solo hilo (el hilo principal), lo cual puede causar bloqueos en la interfaz de usuario cuando se realizan tareas intensivas. Para optimizar el rendimiento y evitar estas interrupciones, se introdujo la API de Web Workers, que permite ejecutar scripts en hilos separados. Esto permite que tareas pesadas se realicen en segundo plano sin afectar la interacción con el usuario (Simpson, 2023; Pajuelo & Verdú, 2016).
</p> 

## 1\. ¿Qué son los Web Workers?
<p align="justify">
Los Web Workers son scripts de JavaScript que se ejecutan en segundo plano, de manera independiente del hilo principal y sin acceso directo al DOM ni a las variables del contexto global. Su objetivo principal es realizar tareas computacionalmente costosas sin afectar el rendimiento de la interfaz (W3C, 2009; Isaacs & Burns, 2013).
</p>
<p align="justify">
En otras palabras, un Web Worker funciona como un “trabajador” independiente que recibe instrucciones, realiza su tarea y devuelve los resultados, mientras el hilo principal sigue atendiendo a la interfaz.
</p> 

## 2\. Tipos de Web Workers  
### 2\.1 Dedicated Workers
><div align="justify">
>Los Dedicated Workers son aquellos que están vinculados a una única página o script. Una vez que la página se cierra, el worker también finaliza. Son ideales       para tareas exclusivas de una sola aplicación y ayudan a mantener separadas las operaciones intensivas del hilo principal (Isaacs & Burns, 2013).
></div>  

### 2\.2 Shared Worker
><div align="justify">
>Los Shared Workers permiten que múltiples contextos (pestañas, iframes o scripts) del mismo origen compartan un único worker. Esto es útil para coordenar datos o procesos comunes entre varias partes de la aplicación (similares en modelo a los Dedicated Workers, pero con alcance compartido) (Krauweel & Jongmans, 2017).
></div>  

### 2\.3 Service Workers
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

## 3\. Características principales de los Web Workers
- **Ejecución en paralelo:**  Funcionan en hilos independientes, lo que permite procesar tareas largas sin congelar la interfaz (Simpson, 2023).
  
- **Comunicación mediante mensajes:** La interacción con el hilo principal se realiza mediante postMessage y onmessage, lo cual es totalmente asincrónico (W3C, 2009).
- **Entorno aislado:** No comparten el contexto global (como window o document) con el hilo principal (Isaacs & Burns, 2013).
  
- **Acceso a APIs específicas:** Aunque no tienen acceso al DOM, pueden consultar recursos como fetch, XMLHttpRequest, IndexedDB y temporizadores (setTimeout, setInterval) (MDN, 2025).
  
- **Política de seguridad (Same-Origin Policy):** Sólo pueden ejecutar scripts del mismo origen, para evitar riesgos de seguridad (W3C, 2009).
## 4\. Beneficios de usar Web Workers 
- **Mejor rendimiento general:** Permiten aprovechar múltiples núcleos del procesador y evitar el bloqueo del hilo principal (Pajuelo & Verdú, 2016).
- **Experiencia de usuario fluida:** Mantienen la interfaz responsiva mientras se ejecutan procesos pesados (Simpson, 2023).
- **Eficiencia en procesamiento intensivo:** Son ideales para tareas como renderizado gráfico, análisis de datos o cálculos complejos (Simpson, 2023).
- **Optimización de recursos:** Están diseñados para encontrar el balance óptimo entre desempeño y consumo de memoria (Pajuelo & Verdú, 2016).
                                                             <img src="https://i.ytimg.com/vi/Gcp7triXFjg/maxresdefault.jpg" width="100" align="right">
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
>Este ejemplo muestra cómo un Web Worker puede realizar un cálculo muy grande (suma de números hasta 1e8) sin bloquear la página. Mientras el worker procesa, el usuario puede seguir usando la interfaz y no se queda congelada. Demuestra el beneficio principal de los workers: mantener la experiencia fluida.

## 5\. Limitaciones de los Web Workers

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
>Aquí el worker calcula una suma, pero no puede cambiar el contenido del HTML directamente. En su lugar, envía el resultado al hilo principal, y este sí actualiza la página. Esto demuestra la limitación de los workers: no tienen acceso al DOM, solo pueden comunicarse mediante mensajes.

## 6\. Casos de uso comunes
Los Web Workers se aplican en situaciones donde el procesamiento intensivo es inevitable:

- **Procesamiento de datos masivos:**  Ideal para análisis en el navegador en grandes volúmenes de información (Simpson, 2023).
  
- **Manipulación de imágenes o gráficos:** Aplicación de filtros, compresión o renderizado sin congelar la interfaz (Simpson, 2023).
  
- **Cálculos intensivos:** Simulaciones, criptografía o IA básica funcionan mejor en segundo plano (Simpson, 2023).
  
- **Procesamiento de archivos:** Lectura, compresión y descompresión sin afectar la experiencia del usuario (Simpson, 2023).
<p align="center">
  <img src="https://web.dev/static/articles/workers-overview/image/diagram-showing-links-be-3f8d21126eb87.png" width="400">
</p>

## 7\. Diferencia entre Web Workers y el Main Thread
<p align="justify">
El Main Thread (hilo principal) es el encargado de manejar todo lo que el usuario ve y toca en la aplicación: botones, menús, animaciones y en general la interfaz gráfica. El problema es que, si en este mismo hilo se ejecutan tareas muy pesadas (como cálculos matemáticos grandes o procesar imágenes), la página puede quedarse "congelada" o lenta, ya que el navegador no logra responder a las interacciones a tiempo.
</p>
<p align="justify">
En cambio, los Web Workers funcionan como ayudantes que trabajan en segundo plano. Ellos se encargan de esas tareas complejas mientras el hilo principal se queda libre para responder al usuario. De esta manera, la aplicación sigue siendo rápida y fluida, sin pausas incómodas. En otras palabras, el Main Thread es como el recepcionista de una oficina (atiende al público), mientras que los Web Workers son empleados en otras salas que resuelven los cálculos complicados sin interrumpir la atención al cliente.
</p>

<p align="center">
  <img src="https://bitsofco.de/img/AfltGEBT6F-566.jpeg" width="400">
</p>

 <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ46v-XvAruQJu0MJ47JttB4XDLmPi6xkEZOw&s" width="60" align="right">
 
## ALTO RENDIMIENTO EN JAVASCRIPT
<p align="justify">
JavaScript es un lenguaje dinámico y flexible, ampliamente adoptado para crear aplicaciones web interactivas. Sin embargo, su naturaleza hace que sea propenso a problemas de rendimiento en entornos complejos. Por ello, comprender y aplicar técnicas avanzadas de optimización es esencial para mantener aplicaciones eficientes y escalables (Vepsäläinen et al., 2024)
</p>

2. ¿Qué significa “alto rendimiento”?

Se refiere a lograr tiempos de carga rápidos, ejecución fluida, bajo uso de CPU/memoria y experiencia de usuario sin demoras perceptibles. Optimizar JavaScript no es solo escribir código más rápido, sino también reducir recursos utilizados y evitar cuellos de botella (Vepsäläinen et al., 2024) 

3. Técnicas avanzadas de optimización
   
3.1 Eliminación de código muerto ("dead code")

Muchas páginas cargan librerías o funciones que no usan realmente en producción. Según Kupoluyi et al. (2021), aproximadamente el 70 % de las funciones en una página promedio no se ejecutan y eliminarlas puede reducir hasta un 60 % el tamaño de descarga, acelerando el tiempo de carga en un 25–30 % 

3.2 Optimización JIT y compilación especializada

Al trabajar con motores JavaScript modernos, técnicas como el typed object shapes y la especialización de bloques básicos permiten reducir pruebas de tipo dinámicas e incrementar la velocidad de ejecución en alrededor de un 25 % (Chevalier-Boisvert & Feeley, 2015) 

3.3 Descomposición de tareas largas

Dividir tareas largas en fragmentos más cortos evita bloquear el main thread. Según MDN (2025), esto mejora significativamente la fluidez de la interfaz al evitar interrupciones perceptibles durante la ejecución del código pesado 

3.4 Limpieza de solicitudes y minificación

Reducir el número de archivos JavaScript y minificarlos (eliminar espacio, comentarios, variables innecesarias) disminuye la latencia de carga y el procesamiento inicial con impacto directo en rendimiento (Microsoft, 2023) 

3.5 Uso de analíticas y métricas de rendimiento

Medir es el primer paso para optimizar. Herramientas como Lighthouse y las DevTools del navegador permiten detectar qué scripts tardan más en cargar, qué funciones generan repaints o delays, y qué fragmentos conviene refactorizar (Vepsäläinen et al., 2024)

4. Beneficios del alto rendimiento

Mejora experiencia de usuario: interacciones rápidas y sin bloqueos, especialmente en dispositivos móviles o con red lenta.

Eficiencia de recursos: menos CPU, memoria y ancho de banda gastados.

Ventaja competitiva: páginas más rápidas retienen mejor a los usuarios y mejoran métricas de negocio.

5. Limitaciones y consideraciones

Complejidad técnica: aplicar técnicas como eliminación de código muerto o especialización de JIT requiere herramientas avanzadas o conocimiento profundo de compiladores y análisis estático.

Riesgo de errores: eliminar funciones sin pruebas exhaustivas puede romper funcionalidades invisibles.

Compatibilidad: algunas optimizaciones avanzadas dependen de navegadores modernos y pueden no aplicarse en todos los entornos.

## 8\. Conclusión
<p align="justify">
Los Web Workers se han convertido en una herramienta muy útil para las aplicaciones web modernas, sobre todo cuando se necesita trabajar con procesos intensivos sin afectar la experiencia del usuario. Gracias a ellos, las páginas pueden ejecutar múltiples tareas al mismo tiempo: el usuario sigue navegando, clicando o escribiendo, mientras los cálculos pesados se realizan aparte.
</p>
<p align="justify">
Aunque presentan algunas limitaciones, como no poder acceder directamente al DOM o el consumo adicional de memoria cuando se usan muchos, los beneficios superan ampliamente estos inconvenientes. Una implementación correcta permite crear aplicaciones más rápidas, responsivas y escalables, que aprovechan al máximo la capacidad de los procesadores actuales.
</p>
<p align="justify">
En resumen, los Web Workers son un aliado clave para los desarrolladores que buscan mejorar tanto el rendimiento como la usabilidad de sus proyectos web, especialmente en aplicaciones que manejan datos grandes, gráficos o requieren multitarea de forma constante.
</p>
<p align="justify">
El alto rendimiento en JavaScript avanzado se apoya en técnicas que van más allá de mejorar un bucle o usar async. Se trata de medir, identificar cuellos de botella y usar patrones sofisticados como eliminación de dead code, optimización de motores JIT, y fragmentación de tareas. Aunque requieren trabajo adicional, estos enfoques devuelven enormes beneficios en velocidad, experiencia y eficiencia. Aplicar estos métodos posiciona tus aplicaciones como más robustas, escalables y amigables para el usuario.
</p>

## 7\. Referencia
-	Kaluvakuri, S., & Vadiyala, V. R. (2016). Harnessing the potential of CSS: An exhaustive reference for web styling. Engineering International, 4(2), 95–110. https://doi.org/10.18034/ei.v4i2.682
-	World Wide Web Consortium (W3C), Selectors Level 3. W3C Recommendation, 2018. [Online]. Available: https://www.w3.org/TR/selectors-3/
-	K. J. Grant, CSS in Depth. Shelter Island, NY: Manning Publications, 2017. [Online]. Available: https://www.manning.com/books/css-in-depth
-	M. Haverbeke, Eloquent JavaScript: A Modern Introduction to Programming, 3rd ed. San Francisco, CA: No Starch Press, 2018. [Online]. Available: https://eloquentjavascript.net/
-	MDN Web Docs, Specificity - CSS cascade. Mozilla Developer Network, 2024. [Online]. Available: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascade/Specificity
-	FreeCodeCamp, What is CSS Specificity?, 2021. [Online]. Available: https://www.freecodecamp.org/news/what-is-css-specificity/
-	GeeksforGeeks, Explain the concept of specificity in CSS, 2022. [Online]. Available: https://www.geeksforgeeks.org/css/explain-the-concept-of-specificity-in-css/
-	MDN Web Docs, CSS: Unidades de medida. Mozilla Developer Network, s.f. [Online]. Available: https://developer.mozilla.org/es/docs/Web/CSS/length 
-	MDN Web Docs. (2024). Positioning in CSS. Mozilla Developer Network. https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Positioning 
-	Jesusda. (s. f.). Introducción a CSS. Recuperado de https://www.jesusda.com/docs/ebooks/introduccion_css.pdf
-	Ventics. (s. f.). Margen y relleno en CSS. Recuperado de https://ventics.com/margen-y-relleno-en-css/ 
-	W3Schools. (s. f.). Modelo de caja en CSS. W3Schools. Recuperado de https://www-w3schools-com.translate.goog/css/css_boxmodel.asp?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc 
-	George, L. (2020, junio 10). The theory of the box model: Margin and padding explained. Hashnode. Recuperado de https://laviedegeorge.hashnode.dev/the-theory-of-the-box-model-margin-and-padding-explained-ck88j5cgx00067rs1g4b1b6v5 
-	MDN Web Docs. (s. f.). Introduction to the CSS box model. Mozilla. Recuperado de https://developer-mozilla-org.translate.goog/en-US/docs/Web/CSS/CSS_box_model/Introduction_to_the_CSS_box_model?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=sge#:~:text=Every%20box%20is%20composed%20of,border%20edge%2C%20and%20margin%20edge 
-	Oregoom. (s. f.). CSS color. Oregoom. Recuperado de https://oregoom.com/css/color/


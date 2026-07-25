# CyanoLUT

**Generador de cuña escalonada y curva de corrección para cianotipia.**

*[English version](README.md)*

Un único archivo HTML autónomo. Sin servidor, sin instalación, sin que ningún dato salga del navegador. Se abre y funciona — también sin conexión, salvo la exportación a PDF, que carga jsPDF desde un CDN.

Disponible en español, inglés, francés y euskera (ES / EN / FR / EU).

---

## Qué hace

La cianotipia no responde de forma lineal a la luz. Un negativo que en pantalla parece perfectamente graduado sale con las luces aplastadas y las sombras cerradas. La solución habitual es aplicar una curva de corrección (LUT) al negativo antes de imprimirlo — pero esa curva hay que *medirla*, porque es propia de tu cadena concreta: tu impresora, tu poliéster, tu foco UV, tu sensibilizador, tu agua.

CyanoLUT la mide, en dos pestañas:

**01 · Generar cuña** — Produce una cuña escalonada de 21 o 31 pasos, lineal en valor de gris, dispuesta para llenar un A4. Se descarga en PDF (tamaño real, sin escalado) o PNG, y se imprime como negativo sobre poliéster/acetato.

**02 · Analizar y curva** — Cargas el escaneo de la copia ya revelada y seca. Marcas cada columna con dos clics (primer y último paso). La herramienta mide la respuesta del proceso y construye la curva de corrección inversa, exportable a `.cube`, `.acv` y `.csv`.

Ese LUT se aplica a tus futuros negativos, en escala de grises, antes de imprimirlos.

---

## Flujo de trabajo

1. **Encuentra primero el tiempo de exposición.** CyanoLUT no lo calcula. Usa el método del opaco moviéndose por franjas en intervalos iguales, con el poliéster limpio encima para incluir su densidad base.
2. **Genera e imprime la cuña** a tamaño real sobre poliéster.
3. **Expón y revela** exactamente como en producción.
4. **Seca ≥24 h.** El azul de Prusia sigue intensificando por oxidación.
5. **Escanea con toda corrección automática desactivada.**
6. **Carga el escaneo**, selecciona el *mismo* número de pasos que generaste, ajusta la ventana de medición y marca las columnas de izquierda a derecha.
7. **Revisa la curva** y exporta.
8. **Aplica el LUT** a un negativo real en escala de grises, imprime, expón. La copia corregida debe repartir el tono mucho mejor.

---

## Decisiones de diseño que conviene conocer

**Mide el canal rojo.** El azul de Prusia absorbe fuerte en el rojo, así que es ahí donde el pigmento modula de verdad. Medido sobre el mismo escaneo, el rojo dio un 49 % más de rango de densidad que la luminosidad, aproximadamente el doble de discriminación en las sombras profundas, y una curva cruda más limpia (menos retrocesos de monotonía). La temida saturación en sombras no se materializa con el Dmax real del proceso. Elegido un canal, no lo mezcles: dos LUT medidos en canales distintos no son comparables.

**Mediana, no media.** El muestreo toma la mediana sobre una ventana rectangular proporcional al parche, para que el polvo, las manchas de revelado y la suciedad del escáner no arrastren la lectura.

**PAVA + PCHIP, sin modelo paramétrico.** La regresión isotónica fuerza la monotonía; una spline monótona de Fritsch–Carlson la convierte en curva continua, muestreada a 256 puntos. Se probó un modelo de saturación Weibull y se descartó: no capta la forma en S del proceso (residuo estructural de hasta ~0,167 en densidad) y sus parámetros degeneran. Cuando no hay curva teórica establecida, asumir solo monotonía es la opción honesta.

**Banda de sacrificio (impresoras láser).** Barra negra opcional sobre el bloque de parches. La láser sobredeposita tóner en la primera franja de la hoja que entra al fusor — fenómeno genérico de la electrofotografía, amplificado sobre PET no absorbente. Sin la banda, el parche superior de cada columna sale más claro de lo que le toca y la curva muestra una caída no monótona. La banda se lleva el golpe en su lugar. En inkjet no hace falta (no hay fusor), por eso viene desactivada por defecto.

**Descartar puntos es un parche, no una solución.** Puedes hacer clic en un punto del panel de respuesta para excluirlo; se reconstruye por interpolación lineal entre vecinos sanos. Úsalo solo cuando puedas nombrar la causa física (borde de fusor, coating irregular, una gota de agua). La diferencia entre «este punto está corrupto por X» y «este punto rompe la suavidad» es la que separa calibrar de maquillar datos.

**Las mesetas indican sobreexposición, no infraexposición.** Si varios pasos miden *exactamente* la misma densidad, estás saturando los parches más transparentes. Contraintuitivo pero medido: reducir el tiempo separó los extremos y *subió* el Dmax medido. Invertir una curva con un tramo plano produce un salto en el LUT — matemáticamente inevitable, y visible como posterización en la copia. Se corrige en la exposición; el homogeneizado solo lo enmascara.

---

## Requisitos

Un navegador. Nada más. jsPDF se carga desde un CDN únicamente para exportar el PDF; si no carga, la exportación a PNG sigue funcionando sin conexión.

---

## Validación

El bucle completo se ha validado en la práctica con 21 pasos: generar → exponer → medir → generar LUT → aplicarlo a un negativo real → reimprimir, con la curva de respuesta acercándose visiblemente a la diagonal ideal y recuperando detalle en sombra que sin corregir desaparecía.

Condiciones de la calibración de referencia: 8′30″ de exposición, poliéster Avery PET, LED UV 365 nm en columna de ampliadora, oxidación forzada con peróxido, canal rojo, escaneo plano sin ajustes.

El modo de 31 pasos está implementado y verificado en pantalla, pero aún no validado sobre papel.

**Un LUT solo es válido mientras la cadena que lo produjo no cambie** — impresora, tóner, poliéster, fuente de luz, sensibilizador, química. Si cambia cualquiera de ellos, hay que recalibrar.

---

## Licencia

MIT © 2026 Alberto Areta

---

## Del mismo autor

[Negativo Lab](https://github.com/alareta/NEGATIVO-LAB) — herramienta libre de revelado de negativos fotográficos en el navegador.

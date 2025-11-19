Ayudante encargado: Joaquín Barros

Estudiante: Baltazar Pellizzon

## Proyecto-vanadio
En este repositorio, se buscará implementar un modelo 1D para baterias de Vanadio. Para código Python.

Entonces para comenzar nuestra modelación tenemos la siguiente ecuación maestra:

$$\frac{d}{dt} [V(t) \cdot C_k(t)] = Q_{in}(t) \cdot C_{k,in}(t) - Q_{out}(t) \cdot C_k(t) + V(t) \cdot R_{k,hom}(t) + \dot{n}_{k,cell} + \dot{n}_{k,mem}(t) + \dot{n}_{k,add}(t)$$ 

[1,3,4,5]

La ecuación esta escrita de tal forma que represente:

Acumulación = Entrada - Salida + Generación + Externalidades

Luego, si desglosamos la derivada por regla de la cadena tenemos,

$$\frac{d}{dt} [V(t) \cdot C_k(t)] = V(t) \cdot \frac{dC_k(t)}{dt} + C_k(t) \cdot \frac{dV(t)}{dt}$$

Desarrollando,

$$V(t) \cdot \frac{dC_k(t)}{dt} + C_k(t) \cdot \frac{dV(t)}{dt} = Q_{in} \cdot C_{in} - Q_{out} * C_k + V \cdot R_hom + \dot{n}_{cell} + \dot{n}_{mem}$$

Luego pasamos restando y obtenemos nuestra ecuación explicita,

$$\frac{dC_k}{dt} = \frac{1}{V(t)} \cdot [(Q_{in} \cdot C_{in} - Q_{out} \cdot C_k + \dot{n}_{cell} + \dot{n}_{mem}) - C_k(t) \cdot \frac{dV}{dt}]$$

De acá, los términos para caudal se van a 0 si asumimos que el sistema es estable en el tiempo, es decir, que las bombas funcionan correctamente.

De esta manera tenemos,

$$\frac{dC_k}{dt} = \frac{1}{V(t)} \cdot ((\dot{n}_{cell} + \dot{n}_{mem}) - C_k(t) \cdot \frac{dV}{dt})$$

Entonces $$\frac{dC_k}{dt}$$ es el cambio en la concentración para la especie k. Luego, $$\dot{n}_{cell}$$ es la producción o consumo neto de la especie k por la reacción electroquímica de la celda. $$\dot{n}_{mem}$$ es el flujo neto para la especie k en la membrana.
$$C_k(t) \cdot \frac{dV}{dt}$$ es el ajuste para el cambio de volumen del estanque. Por último, $$\frac{1}{V(t)}$$ podemos tomarlo como un ajuste para pasar de moles a concentración.

Ahora como se define explicitamente el cambio de volumen. Podemos definirlos por estanque de la siguiente forma:

$$\frac{dV_{neg}(t)}{dt} = -A_{mem} \cdot J_{total_vol}(t) $$

Y,

$$\frac{dV_{pos}(t)}{dt} = +A_{mem} \cdot J_{total_vol}(t) $$

[3]

Aquí, $$A_{mem}$$ es el área de membrana (igual a área de celda). Donde $$J_{total_vol}(t)$$ se ve afectado por dos fenómenos físicos:

- Arrastre electro-osmótico

$$J_{eo}(t) = \frac{n_d \cdot I(t)}{F} \cdot \overline{V}_{H2O}$$

[3]

- Ósmosis

$$J_{osm}(t) = K_{osm} \cdot (C_{total}^{pos}(t) - C_{total}^{neg}(t)) \cdot \overline{V}_{H2O}$$

[3]

Lo que nos permite definir $$J_{total_vol}$$ como:

$$J_{total_vol} = \overline{V}_{H2O} \cdot (\frac{n_d \cdot I(t)}{F} + K_{osm} \cdot (C_{total}^{pos}(t) - C_{total}^{neg}(t)))$$

[3]

En estas ecuaciones, $$n_d$$ son los moles de agua arrastrado por cada mol de carga. $$I(t)$$ es la corriente eléctrica instantánea. F es constante de Faraday. $$\overline{V}_{H2O}$$ es el volumen molar del agua. Por otro lado, $$K_{osm}$$ es el coeficiente osmótico proporcional a la permeabilidad de la membrana. Ambas $$C_{total}$$ representan concentraciones. 

De esta forma la ecuación para el cambio en el volumen queda de la siguiente manera:

$$\frac{dV(t)}{dt} = A_{mem} \cdot \overline{V}_{H2O} \cdot (\frac{n_d \cdot I(t)}{F} + K_{osm} \cdot (C_{total}^{pos}(t) - C_{total}^{neg}(t)))$$

Ahora bien, $$\dot{n}_{cell}$$ se define como:

$$\frac{v_k \cdot I(t)}{F}$$

por Ley de Faraday. Donde $$v_k$$ es el coeficiente estequimétrico de la especie k.

Por otro lado, tenemos que definir $$\dot{n}_{k,mem}$$.

Si tomamos el negativo del flujo molar podemos tener algo del estilo:

$$\dot{n}_{k,mem}^{neg}$$

El cual se define como:

$$\dot{n}_{k,mem}^{neg} = -A_{mem} \cdot J_k$$

Donde J_k es un valor determinado por 3 fenómenos distintos: Difusión, Migración Eléctrica y Convección. Esto es,

$$J_k = - \frac{D_k}{\delta} \cdot (C_k^{pos} - C_k^{neg}) - \frac{z_k \cdot D_k}{R \cdot T \cdot \delta} \cdot C_{k,avg} \cdot \Delta \phi + C_{k,avg} \cdot J_{total_vol}$$

[3, 5] 

$$D_k$$ es difusividad de la especie k en la membrana. $$\delta$$ es el espesor efectivo de la membrana. $$(C_k^{pos} - C_k^{neg})$$ es la diferencia de concentraciones entre ambos lados, fuerza impulsora de difusión. $$z_k$$ es la carga del ión k, dirección de movimiento en un campo eléctrico. $$\Delta \phi$$ es la diferencia de potencial eléctrico a través de la membrana, lo que genera migración iónica. $$C_{k,avg}$$ es concentración promedio para la especie k en la membrana. Por cierto, trabajaremos a una T estable de 25° grados asumiendo que la batería se encuentra bajo refrigeración o que se calienta por el aire ambiente. 

Por último, $$C_{k}^{neg} \cdot \frac{dV_{neg}(t)}{dt}$$ es una expresión basada en cosas que ya hemos discutido. Por lo cual, su expresión explicita es:

$$C_{k}^{neg} \cdot \frac{dV_{neg}(t)}{dt} = -A_{mem} \cdot C_k^{neg}(t) \cdot \overline{V}_{H2O} \cdot [\frac{n_d \cdot I(t)}{F} + K_{osm} \cdot (C_{total}^{pos}(t) - C_{total}^{neg}(t))]$$

[3]

Para el voltaje trabajaremos con Nernst, como,

$$E_{OCV}(t) = E^{0} + \frac{R \cdot T}{n \cdot F} \cdot \ln(\frac{[{V_O}_2^{+}]_{pos} \cdot [V^{2+}]_{neg} \cdot [H^{+}]^{2}_{pos}}{[{VO}^{2+}]_{pos} \cdot [V^{3+}]_{neg}})$$

[1,3,4,5]

$$E_{OCV}(t)$$ es el potencial de celda para el circuito abierto en un tiempo t. $$E^{0}$$ es el potencial estándar de la celda, se deriva de los potenciales estándar de ambas semirreacciones. Luego, simplemente esta el factor Nernst $$\frac{R \cdot T}{n \cdot F}$$, que considera constantes, T absoluta y número de electrones transferidos (en ambas semi-reacciones es 1). Y luego, están las concetraciones para las especies de vanadio, tanto en el lado positivo como en el negativo. Por otro lado, $$[H^{+}]^{2}_{pos}$$ 

viene de la semireacción 

$$[{V_O}_2^{+}]_{pos}/[{VO}^{2+}]_{pos}$$

donde su estequimetría es de 2.

Si consideramos resistencia Ohmica, tendremos que el potencial de la celda se define como:

$$E_{cell}(t) = E_{OCV}(t) +- I(t) \cdot R_{total}$$

[1]

Luego, para los protones tendremos este equilibrio secundario. Aparentemente es mejor plantear la disociación del ácido súlfurico de esta manera porque se disocia extremadamente rápido, así que un término empírico puede ocasionar malos resultados.

Entonces, el equilibrio secundario queda como,

$$HSO_4^{-} \leftrightarrow $$ H^{+} + SO_4^{2-}$$

Donde esta ecuación se ve regida por la siguiente constante de equilibrio:

$$K_2 = \frac{[H^{+}]_{libre} \cdot [SO_4^{2-}]}{[HSO_4^{-}]}$$

A su vez, podemos plantear el balance de masa considerando electroneutralidad y $$K_2$$. Así, obtenemos una ecuación cuadrática en términos de concentración de protón libre, donde x = $$[H^+]_libre$$,

$$x^2 + (C_{s,total} - C_{H,total} - K_2) \cdot x - K_2 \cdot C_{H,total} = 0$$

Este es el valor de concentración de protón libre que se reemplazaría en la expresión de Nernst anteriormente explicada. 

Podemos verificar que este valor esta correcto en la implementación del código, mediante la siguiente tabla. Disponible en: https://ibero.mx/campus/publicaciones/quimanal/pdf/tablasconstantes.pdf. Revisarse Ka2 para súlfurico.

Y acoplando obtenemos $$E_cell$$

Detalles:

- Se trabajo en modo galvanostático, ya que la modelación potenciostática no logro darme correctamente. Por ejemplo, la carga de la batería caia más rapido a mayor SOC que a menor. Por otro lado, no se considero el equilibrio de Donnan para los protones libres, ya que el valor $$K_2$$ parece ser robusto. De todas manera, esta opción sigue disponible en caso de necesitar definir los protones libres de manera más precisa.
  

Referencias:

[1] Modelo Cinético (Knehr et al.)

[1] K. W. Knehr et al., "A Transient Vanadium Flow Battery Model Incorporating Vanadium Crossover and Water Transport through the Membrane," Journal of The Electrochemical Society, vol. 159, no. 9, pp. A1446-A1459, 2012.

[2] Parámetro de Resistencia (Zhou et al.)

[2] X. L. Zhou et al., "Performance of a vanadium redox flow battery with a VANADion membrane," Applied Energy, vol. 180, pp. 353–359, Oct. 2016. Disponible: https://doi.org/10.1016/j.apenergy.2016.08.001

[3] Modelado de Transporte y Volumen (Wang et al.)

[3] W. Wang et al., " A new zero-dimensional dynamic model to study the capacity loss mechanism of vanadium redox flow batteries," Journal of Power Sources, vol. 603, 234428, 2024. Disponible: https://doi.org/10.1016/j.jpowsour.2024.234428

[4] Termodinámica del Electrolito (Gandomi et al. o similares)

[4] Y. A. Gandomi et al., " In Situ Potential Distribution Measurement anD Validated Model for All-Vanadium Redox Flow Battery," Journal of The Electrochemical Society, vol. 163, no. 1, pp. A5188-A5201, 2016.

[5] Ecuaciones y fundamentos teóricos.

[5] F. Huerta, Formulario IIQ3843 — Procesamiento de hidrógeno para energías sostenibles, Escuela de Ingeniería, Pontifica Universidad Católica de Chile, Santiago, Chile, 2025.

[6] Consultas, orden y verificación por Gemini AI por Google.

[6] Gemini. (2025, Noviembre). Consulta conversacional sobre modelo de simulación de VRFB. Google. [En línea]. Disponible: google.com/gemini




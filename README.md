Ayudante encargado: Joaquín Barros

Estudiante: Baltazar Pellizzon

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

En estas ecuaciones, $$n_d$$ son los moles de agua arrastrado por cada mol de carga. $$I(t)$$ es la corriente eléctrica instantánea. F es constante de Faraday. $$\overline{V}_{H2O}$$ es el volumen molar del agua. Por otro lado, $$K_{osm}$$ es el coeficiente osmótico proporcional a la permeabilidad de la membrana. Ambas $$C_{total}$$ representan concentraciones. 

De esta forma la ecuación para el cambio en el volumen queda de la siguiente manera:

$$\frac{dV(t)}{dt} = A_{mem} \cdot \overline{V}_{H2O} \cdot (\frac{n_d \cdot I(t)}{F} + K_{osm} \cdot (C_{total}^{pos}(t) - C_{total}^{neg}(t)))$$




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












































############################# ANTIGUO

## Proyecto-vanadio
En este repositorio, se buscará implementar un modelo 1D para baterias de Vanadio. Para código Python.

### 0DVanadio

En la primera parte de este proyecto se trabajará de acuerdo al balance de materia para un reactor CSTR, el cual viene dado por la siguiente expresión:

dC/dt = Q/V_t * (C_m - C) + r

donde la parte izquierda de la suma viene a representar el término convectivo, mientras que r se representará de acuerdo a la ley de Faraday. De la siguiente manera:

\dot{n} = I/(n_e/s_i * F) es la Ley de Faraday (Formulario I2, 2025), luego como el número de electrones intercambiado en las semireacciones del Vanadio es de 1 (Gandomi, 2016) y los coeficientes estequiométricos también es de 1. La expresión nos queda como:

\dot{n} = I/(F)

Luego, tenemos que en el volumen del tanque t, el cambio de concentración es:

dC/dt = \dot{n}/V_t

Por lo que si reemplazamos por la expresión simplificada de la Ley de Faraday que obtuvimos antes, se tiene,

dC/dt = I/(F * V_t)

donde si la especie se consume (oxida) es de signo negativo. Y si se reduce/produce es de signo positivo. 

Entonces, esto estará definido en dos funciones: anode_tank y cathode_tank.

El volumen se fijó en 60mL para cada estanque (Knehr, 2012).

Lo que son 6 * 10^{-5} m3.


Por otro lado, el tiempo de residencia se define de acuerdo al caudal, el cual es:

30 mL/min (Knehr, 2012) => 0,03 L/min => 5 * 10^{-4} L/s => Q =  5 * 10^{-7} m3/s

Luego, la expresión para el tiempo de residencia es:

\tau = V_t/Q,

Así, el tiempo de residencia es de 120 segundos, o 2 minutos.


Luego, el tiempo de descarga viene dado por la siguiente expresión:

t = (n * F * C_tot * V_t * \Delta SOC) / I    (Expresión derivada de la Ley de Faraday)

donde I es una rampa lineal, es decir,

I(\overline{t}) = (10 + 2) / 2

donde C_tot es 1000 mol/m3 (suma de especies iniciales positivas y negativas) (Gandomi, 2016). Por otro lado, el \Delta SOC es 60% ya que se considera 80% como máxima carga y 20% como descargada. 

De esta manera, podemos fijar el tiempo de descarga en unos 579 segundos; es decir, 9 minutos y 39 segundos, para una intensidad de corriente de 10 A bajando hasta 2 A de manera lineal.

Por otra parte, el área de celda se calcula como:

A_{cell} = l_{cell} * w_{cell} = 0,0224 * 0,03 = 6,72 * 10^{-4} m2     (Gandomi, 2016)

Más adelante, se verá que se definen concentraciones iniciales y también la función RHS, la cual nos devuelve las 4 derivadas, una para cada especie en su respectivo estanque.

Luego, se integra y se entrega un resumen.

Este modelo 0D puede ofrecer los siguientes resultados:

- Evolución de las concentraciones de Vanadio para cada estanque
- Estado de carga de cada estanque
- Estados finales

Aún se puede mejorar considerando los protones, corrección posible mediante Donnan. (Aún no implementado)

El siguiente paso que se quiere realizar es poder predecir el voltaje dinámico y la eficiencia de energía.

Para integrarlo con el modelo 1D, se necesita implementar:

- Ecuaciones de transporte en el electrodo
- Definir la densidad de corriente de acuerdo al modelo Butler-Volmer, que depende del sobrepotencial (Aún no implementado)
- Aplicar Ley de Ohm
- Condiciones de borde, ya con ecuaciones de balance local
- Se necesitará resolver con una resolución espacial
  
Para finalizar este avance se muestra el gráfico para la evolución del estado de carga de la batería.

### D_corriente

Aplicación práctica para potenciales de equilibrio, Butler-Volmer y densidad de corriente dependiente de concentraciones. Necesario para implementar a futuro. Útil para generar modelo potenciostatico.



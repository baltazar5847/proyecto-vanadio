Ayudante encargado: Joaquín Barros

Estudiante: Baltazar Pellizzon


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



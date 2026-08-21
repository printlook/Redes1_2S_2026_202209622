# Informe de Desarrollo
## Práctica 1 — QuetzalDev S.A.

**Redes de Computadoras 1 — Segundo Semestre 2026**
**Carné:** 202209622
**Universidad de San Carlos de Guatemala — Facultad de Ingeniería**

---

## Proceso de diseño

Lo primero que hice al recibir el plano fue leerlo antes de dibujar nada. El edificio es de un solo nivel, mide 24 m por 23 m, y está organizado en dos alas separadas por un pasillo central de 2 m de ancho por 20 m de largo. En el ala izquierda están Recepción, Recursos Humanos y Legal; en la derecha, Sala de Capacitación, Diseño e Innovación, Dirección General y Backend. Al sur, colgando del bloque principal, están el Vestíbulo de Ingreso y el Data Center.

Esa lectura inicial ya me dio la primera conclusión importante del diseño: el pasillo central es el eje natural de toda la instalación. Todas las oficinas tienen puerta hacia él, así que cualquier ruta de cableado que no pase por ahí termina atravesando cuartos ajenos, lo cual es malo tanto por costo como por mantenimiento.

Después conté los equipos. El enunciado especifica 30 computadoras de escritorio, 12 laptops y 6 servidores, y al sumar las cantidades que asigna a cada departamento me dieron 42 computadoras, que es exactamente 30 + 12. Eso me confirmó que la libertad que da el enunciado no es para cambiar cuántos equipos hay por área, sino para decidir cuáles de esos equipos son de escritorio y cuáles portátiles. Lo menciono porque en algún momento consideré mover equipos entre departamentos y al verificar la suma me di cuenta de que habría roto la consistencia con el enunciado.

El orden de trabajo que seguí fue: ubicar el cuarto de telecomunicaciones, colocar los switches, definir las tomas de red, trazar el troncal, trazar el horizontal, y al final etiquetar. Intenté empezar por las tomas y no funcionó, porque sin saber dónde estaba el MDF no podía estimar ninguna distancia.

## El reto principal: interpretar el plano

El problema más grande que encontré es que **el plano no está dibujado a escala uniforme**. Lo descubrí midiendo: el Departamento de Recepción está rotulado como 10 m x 8 m y en el dibujo ocupa menos altura que Recursos Humanos, que está rotulado 10 m x 6 m. Lo mismo pasa en el ala derecha, donde cuatro cuartos rotulados todos como 12 m x 5 m tienen alturas visiblemente distintas.

Esto me obligó a cambiar el método. Mi idea original era medir en píxeles y convertir con una regla de tres a partir de los 24 m de ancho del edificio. Al ver la inconsistencia, decidí estimar todas las distancias a partir de las **dimensiones rotuladas de cada cuarto**, usando el dibujo solo como referencia de posición relativa. Es menos elegante pero es lo correcto: el rótulo es el dato del arquitecto, el dibujo es una representación.

El segundo reto fue que **el plano no incluye cuarto de telecomunicaciones**. No hay ningún espacio reservado para equipo de red. Esto significaba proponerlo yo, modificando el plano base, cosa que al principio dudé en hacer porque no sabía si estaba permitido alterar la distribución arquitectónica. Al releer el enunciado —que pide "identificar sobre el plano la ubicación del cuarto de telecomunicaciones" procurando minimizar distancias— entendí que proponer el espacio es justamente parte del trabajo del ingeniero.

El tercer reto fue una tensión en los requerimientos que tuve que resolver por criterio. El enunciado exige un switch por departamento, pero también dice que el patch panel del edificio se dimensione según la cantidad total de puntos de red. Si cada departamento tiene su switch, al MDF solo llegan ocho enlaces troncales, no 48. Resolví cumpliendo la regla literal —patch panel de 48 puertos y switch de 48 puertos en el MDF— y documentando que los 40 puertos libres son reserva de crecimiento, con la posibilidad de migrar en el futuro a un esquema de cableado directo desde cada puesto. Prefiero cumplir el requisito y justificar el excedente que reinterpretar el enunciado a mi conveniencia.

## Criterio para ubicar el MDF

Mi primer instinto fue poner el MDF dentro del Data Center. Tiene sentido intuitivo: ya es un cuarto destinado a equipo, tiene control de acceso y ahí están tres de los seis servidores.

Antes de darlo por hecho hice el cálculo, y el resultado me hizo cambiar de opinión. El Data Center está en la esquina sur del edificio, así que un cable hacia Sala de Capacitación —que está en el extremo norte— tendría que recorrer prácticamente todo el largo del inmueble. Sumando los ocho enlaces troncales me dieron unos 132 m. Al evaluar un punto central sobre el pasillo, la misma suma bajó a unos 62 m.

Menos de la mitad de cable, y ningún enlace troncal mayor a 18 m. Con eso decidí proponer un cuarto de 3.0 m x 2.5 m recortado de la esquina noreste de Recursos Humanos, con puerta directa al pasillo. RRHH pierde 7.5 m² de sus 60, lo cual no afecta su operación porque conserva espacio para sus dos mesas de trabajo.

Un beneficio adicional que no había considerado al inicio es que separar el MDF del Data Center mejora la seguridad física: un técnico que entra a dar mantenimiento a los servidores no queda con acceso al equipo de conmutación de todo el edificio.

## Criterio para las topologías

Para los ocho departamentos elegí topología en estrella sin mucha discusión, y creo que es la respuesta honesta. En una LAN de oficinas moderna, con switches de por medio, la estrella es lo que se instala: aísla fallas por puerto, es la más barata por host, y crecer significa simplemente ocupar un puerto libre.

Descarté la malla porque interconectar ocho segmentos exigiría 28 enlaces, un costo desproporcionado en un edificio de un nivel donde todo el tráfico pasa igual por el core. El bus lo descarté porque el medio compartido es punto único de falla y limita el ancho de banda. El anillo requiere protocolos de recuperación adicionales que no aportan nada a esta escala.

A nivel de edificio la topología resultante es un árbol, o estrella extendida jerárquica: un switch core en el MDF alimentando ocho switches de acceso.

La única excepción donde sí pagué redundancia fue el Data Center. Ahí apliqué un criterio distinto porque cambia la variable dominante: no es costo, es disponibilidad. Ese segmento aloja tres de los seis servidores de la empresa, y si su único enlace se cae, se cae el servicio para todo el edificio. Por eso mantuve la estrella hacia los servidores pero dupliqué el uplink hacia el MDF.

## Criterio para el equipo activo

Dimensioné de abajo hacia arriba. Conté los hosts de cada departamento, definí cuántas tomas y de cuántos puertos necesitaba, y de ahí salió el tamaño del patch panel y del switch de cada área. El departamento con más carga es Sala de Capacitación con 10 hosts, así que un switch de 16 puertos cubre todos los segmentos con margen para el uplink y para crecer.

Para el core partí del total: 48 puntos de red, entonces patch panel de 48 y switch de 48 puertos con SFP+ para recibir la fibra.

Los patch panels fueron la decisión que más me costó justificar ante mí mismo, porque a primera vista parecen un gasto que se podría evitar conectando el cable directo al switch. Al investigar entendí el punto: el patch panel existe para que el cable permanente no se manipule nunca. Todos los cambios se hacen moviendo patch cords en el frente del panel, y eso es lo que permite que una instalación certificada conserve su garantía durante veinte años.

## Justificación del medio en el cableado troncal

Para los siete enlaces MDF → switch de departamento elegí UTP Cat 6A, y para el enlace MDF → Data Center fibra multimodo OM4 dúplex.

Evalué tres criterios. La **distancia** resultó no ser restrictiva: ningún troncal de cobre pasa de 18 m, muy lejos del límite de 100 m, así que no fue lo que decidió. El **ancho de banda** sí fue determinante: el uplink de un switch concentra el tráfico de hasta 10 hosts, y si todos transmitieran a 1 Gbps simultáneamente el enlace necesitaría 10 Gbps para no volverse cuello de botella. Cat 6 solo garantiza 10GBASE-T hasta 55 m y con restricciones de diafonía; Cat 6A lo garantiza hasta 100 m. El **costo** terminó siendo casi irrelevante: Cat 6A cuesta alrededor del doble por metro, pero como los troncales suman apenas 45 m, el sobrecosto total es de unos pocos cientos de quetzales. Me pareció una inversión mínima para no tener que recablear el backbone cuando la empresa migre a 10 Gbps.

Para el Data Center la decisión fue distinta y quiero ser honesto en esto: **20 m de Cat 6A habrían funcionado perfectamente**, y a menor costo. Elegí fibra por dos razones que no son de rendimiento puro. La primera es aislamiento eléctrico e inmunidad electromagnética: el Data Center concentra carga eléctrica y su propio respaldo, y la fibra no acopla ruido ni transmite diferencias de potencial entre cuartos. La segunda es que ese enlace es el que más disponible debe estar de toda la red, y al ser un enlace único el sobrecosto es marginal frente al total del proyecto. Además, tener fibra instalada deja la puerta abierta a escalar ese segmento a 25 o 40 Gbps sin tocar la canalización.

## Reflexión final

Lo que más me llevo de la práctica es que el diseño de Capa 1 se parece menos a configurar equipo y más a resolver un problema de geometría con restricciones. Casi ninguna decisión importante tuvo que ver con tecnología de red: dónde poner el MDF fue un problema de minimizar distancias, qué canalización usar fue un problema de acceso para mantenimiento, y qué tipo de toma instalar fue un problema de cómo está distribuido el mobiliario.

También me quedó claro que el cableado estructurado se diseña pensando en los veinte años siguientes, no en el día de la instalación. Casi todas las decisiones que tomé —Cat 6 en lugar de Cat 5e, Cat 6A en el troncal, 16U libres en el gabinete, 40 puertos de reserva en el patch panel— se justifican por lo que va a pasar después, no por lo que se necesita hoy. Reemplazar un switch cuesta una tarde; recablear un edificio cuesta semanas y obra civil.

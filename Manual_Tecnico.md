# Manual Técnico — Diseño de Capa Física
## QuetzalDev S.A. — Edificio corporativo de nivel único

**Curso:** Redes de Computadoras 1 — Segundo Semestre 2026
**Práctica 1:** QuetzalDev S.A.
**Carné:** 202209622
**Universidad de San Carlos de Guatemala — Facultad de Ingeniería**
**Ingeniería en Ciencias y Sistemas**

---

## 0. Supuestos de diseño

Antes de entrar en materia dejo anotados los supuestos que tomé, porque varias decisiones dependen de ellos:

1. El plano proporcionado **no está dibujado a escala uniforme**. Al medirlo se observa que el Departamento de Recepción, rotulado 10 m x 8 m, ocupa menos altura que Recursos Humanos, rotulado 10 m x 6 m. Por lo tanto todas las distancias se estimaron a partir de las **dimensiones rotuladas en cada cuarto**, no de la proporción del dibujo.
2. El edificio no cuenta con un cuarto de telecomunicaciones en el plano original, por lo que se propone su construcción como parte de este diseño.
3. Los 42 equipos de cómputo (30 PC de escritorio y 12 laptops) se distribuyen según la tabla de la sección 1. Las cantidades por departamento son las que fija el enunciado; lo que se definió a criterio propio fue cuáles de esos equipos son de escritorio y cuáles portátiles.
4. Las laptops se conectan por cable a tomas de red fijas. No se contempla infraestructura inalámbrica porque el alcance de la práctica es Capa 1 cableada.
5. Todo el cableado horizontal se poncha bajo el estándar **T568B en ambos extremos** (jack de la toma y patch panel).

---

## 1. Inventario de equipos

### 1.1 Distribución de hosts por departamento

| Departamento | PC escritorio | Laptops | Servidores | Total hosts |
|---|---|---|---|---|
| Recepción | 2 | 1 | 1 | 4 |
| Recursos Humanos | 8 | 0 | 0 | 8 |
| Legal | 4 | 0 | 0 | 4 |
| Sala de Capacitación | 4 | 6 | 0 | 10 |
| Diseño e Innovación | 4 | 3 | 1 | 8 |
| Dirección General | 2 | 2 | 0 | 4 |
| Backend | 6 | 0 | 1 | 7 |
| Data Center | 0 | 0 | 3 | 3 |
| **Total** | **30** | **12** | **6** | **48** |

El criterio para asignar laptops fue la movilidad del puesto: Sala de Capacitación (los participantes rotan), Diseño e Innovación (el equipo de UI/UX trabaja en distintos espacios), Dirección General (los directores se mueven entre reuniones) y un puesto de Recepción. Los departamentos con puestos fijos de trabajo continuo — RRHH, Legal y Backend — quedaron con equipos de escritorio.

### 1.2 Tomas de red por departamento

| Departamento | Tomas | Configuración | Puertos |
|---|---|---|---|
| Recepción | 2 | 2 dobles | 4 |
| Recursos Humanos | 4 | 4 dobles | 8 |
| Legal | 2 | 2 dobles | 4 |
| Sala de Capacitación | 3 | 2 cuádruples + 1 doble | 10 |
| Diseño e Innovación | 4 | 4 dobles | 8 |
| Dirección General | 2 | 2 dobles | 4 |
| Backend | 4 | 3 dobles + 1 unitaria | 7 |
| Data Center | 1 | 1 triple | 3 |
| **Total** | **22** | | **48** |

El criterio para el tipo de toma fue agrupar los puestos de trabajo cercanos en un mismo faceplate. En Sala de Capacitación se usaron tomas cuádruples porque las estaciones están dispuestas en mesas largas con varios puestos contiguos, y una toma de más puertos evita multiplicar cajas sobre la misma pared. En Backend quedó una toma unitaria porque el servidor del departamento está aislado del resto de escritorios.

### 1.3 Inventario general

| Elemento | Cantidad | Descripción |
|---|---|---|
| Switch core | 1 | 48 puertos 10/100/1000 + 4 SFP+, administrable, montaje en rack |
| Switch de acceso | 7 | 16 puertos 10/100/1000 administrables |
| Switch Data Center | 1 | 24 puertos 10/100/1000 + 2 SFP |
| Patch panel MDF | 1 | 48 puertos Cat 6A, 2U |
| Patch panel departamental | 8 | 12 puertos Cat 6, 1U |
| ODF | 1 | 12 puertos LC dúplex |
| Gabinete de piso | 1 | 24U, 600 x 800 mm, cerrado con llave |
| Gabinete de pared | 8 | 6U |
| UPS MDF | 1 | 1000 VA / 700 W, montaje en rack |
| UPS departamental | 8 | 500 VA |
| Faceplates | 22 | 1, 2, 3 y 4 puertos según tabla 1.2 |
| Jacks RJ45 Cat 6 | 48 | Keystone, ponchado T568B |
| Bobina UTP Cat 6 | 3 | 305 m cada una |
| Bobina UTP Cat 6A | 1 | 305 m |
| Fibra OM4 dúplex LC-LC | 2 | 30 m preterminada |
| Transceptores SFP+ SR | 4 | 10 Gbps, multimodo |
| Escalerilla metálica abierta | 22 m | 200 mm de ancho, con accesorios |
| Canaleta plástica 32x12 mm | 100 m | Perimetral en oficinas |
| Organizadores de cable | 3 | 1U horizontales |
| Patch cords Cat 6 | 60 | 1 m y 2 m |

---

## 2. Justificación de la ubicación del cuarto de telecomunicaciones (MDF)

El plano no incluye un cuarto de telecomunicaciones, así que hubo que proponerlo. La ubicación elegida es la **esquina noreste del Departamento de Recursos Humanos**, tomando un espacio de 3.0 m x 2.5 m con puerta directa al Pasillo Central.

La primera opción evidente era usar el Data Center, ya que es un cuarto pensado para equipo. Se descartó tras comparar las distancias, porque está en el extremo sur del edificio:

| Ubicación evaluada | Suma de longitudes troncales |
|---|---|
| Dentro del Data Center | ≈ 132 m |
| Esquina noreste de RRHH (elegida) | ≈ 62 m |

La ubicación elegida reduce a menos de la mitad el cableado troncal. Las razones concretas:

1. **Centralidad geométrica.** El punto queda a media altura del edificio (aproximadamente a 10 m del muro norte y 10 m del sur), de modo que ningún enlace troncal supera los 18 m. Esto deja un margen enorme respecto al límite de 90 m de cableado permanente que fija TIA/EIA-568.
2. **Acceso directo a la canalización principal.** La puerta da al Pasillo Central, que es donde corre la escalerilla metálica. Los cables entran a la bandeja sin atravesar oficinas.
3. **Separación de funciones.** El MDF no debe compartir espacio con los servidores de producción. Manteniéndolos separados, un acceso al Data Center por mantenimiento de servidores no implica acceso al equipo de conmutación del edificio, y viceversa.
4. **Cuarto interior sin ventanas.** Al ser un espacio recortado del interior de RRHH, no tiene muro exterior, lo que reduce exposición a humedad, luz solar directa y accesos no controlados.

El costo de la decisión es que RRHH pierde 7.5 m² de sus 60 m², lo cual no compromete la operación del departamento ya que conserva espacio para sus dos mesas de trabajo.

---

## 3. Justificación de la topología física por departamento

| Departamento | Hosts | Topología | Justificación |
|---|---|---|---|
| Recepción | 4 | Estrella | Segmento pequeño con un servidor de apoyo. La estrella aísla fallas por puerto y el costo por host es bajo. |
| Recursos Humanos | 8 | Estrella | Puestos fijos y sin criticidad operativa. La estrella permite crecer agregando puertos sin tocar el resto. |
| Legal | 4 | Estrella | Segmento pequeño y estático; una topología más redundante no se justifica económicamente. |
| Sala de Capacitación | 10 | Estrella | Alta rotación de equipos. La estrella permite conectar y desconectar sin afectar a los demás puestos. |
| Diseño e Innovación | 8 | Estrella | Incluye servidor de recursos y transferencia de archivos pesados; la estrella dedica ancho de banda por puerto. |
| Dirección General | 4 | Estrella | Pocos hosts, tráfico bajo, prioridad en simplicidad. |
| Backend | 7 | Estrella | Segmento de desarrollo con servidor propio. La estrella evita que la falla de una estación afecte al servidor. |
| Data Center | 3 | Estrella con uplink redundante | Es el segmento más crítico. Se mantiene la estrella hacia los servidores pero el enlace al MDF se duplica en fibra para tolerancia a fallos. |
| **Edificio completo** | 48 | **Árbol (estrella extendida jerárquica)** | Un switch core en el MDF alimenta ocho switches de acceso. Permite crecer por departamento sin rediseñar el núcleo. |

**Por qué no otras topologías.** Se descartó la malla porque con 8 segmentos exigiría 28 enlaces para interconectar todo, con un costo desproporcionado frente al beneficio en un edificio de un solo nivel. Se descartó el bus porque un solo medio compartido crea un punto único de falla y limita el ancho de banda por host. El anillo tampoco aplica: requiere protocolos de recuperación adicionales y no ofrece ventajas en una LAN de esta escala.

La única excepción al criterio de mínimo costo es el Data Center, donde sí se paga redundancia. La razón es que ahí residen tres de los seis servidores del edificio, y la caída de ese único enlace dejaría sin servicios a toda la empresa.

---

## 4. Tipo y categoría de cable por segmento

| Segmento | Medio | Categoría | Distancia máx. estimada | Justificación |
|---|---|---|---|---|
| Horizontal: switch de departamento → tomas de red | UTP | Cat 6 | 17 m | Soporta 1 Gbps hasta 100 m y 10 Gbps hasta 55 m. Las distancias reales no pasan de 17 m, muy por debajo del límite. Cat 5e habría bastado eléctricamente, pero Cat 6 da margen para migrar los puestos a 10 Gbps sin recablear. |
| Troncal: MDF → switches de departamento | UTP | Cat 6A | 18 m | El uplink concentra el tráfico de hasta 10 hosts, por lo que debe soportar 10 Gbps de forma sostenida. Cat 6A garantiza 10GBASE-T hasta 100 m con blindaje contra diafonía externa (alien crosstalk). |
| Troncal: MDF → Data Center | Fibra multimodo | OM4 dúplex LC-LC | 20 m | Es el enlace más largo y el más crítico. La fibra ofrece 10 Gbps con holgura, inmunidad total a interferencia electromagnética, y aislamiento eléctrico entre el MDF y el Data Center. |
| Patch cords en rack y puesto de trabajo | UTP flexible | Cat 6 / Cat 6A | 1 a 2 m | Cable de conductor multifilar, más flexible y resistente a la flexión repetida que el cable rígido de instalación. |

**Nota sobre el uso de fibra.** Podría argumentarse que 20 m de cobre Cat 6A resolvían el enlace al Data Center a menor costo, y es cierto. La decisión de usar fibra se toma por dos razones: el Data Center concentra la carga eléctrica del edificio junto con su propio respaldo, lo que genera un entorno con ruido electromagnético, y porque el enlace debe ser el más disponible de la red. Al ser un enlace único, el sobrecosto es marginal frente al total del proyecto.

---

## 5. Distancias de cableado horizontal y cálculo de bobinas

Las distancias se estimaron siguiendo la ruta real del cable — por canaleta perimetral, no en línea recta — desde el switch de cada departamento hasta cada toma, e incluyen las subidas y bajadas verticales.

| Departamento | Cables | Longitud promedio (m) | Subtotal (m) |
|---|---|---|---|
| Recepción | 4 | 12 | 48 |
| Recursos Humanos | 8 | 11 | 88 |
| Legal | 4 | 10 | 40 |
| Sala de Capacitación | 10 | 13 | 130 |
| Diseño e Innovación | 8 | 12 | 96 |
| Dirección General | 4 | 12 | 48 |
| Backend | 7 | 13 | 91 |
| Data Center | 3 | 5 | 15 |
| **Total** | **48** | | **556** |

**Cálculo:**

```
Longitud base                 = 556 m
Holgura de instalación (10%)  = 55.6 m
Reserva de terminación (1 m por cable x 48) = 48 m
                              -----------------
Total requerido               ≈ 660 m

Bobinas = 660 m / 305 m = 2.16  →  3 bobinas de 305 m
```

Se adquieren **3 bobinas de UTP Cat 6**, quedando aproximadamente 255 m de reserva. Ese sobrante no se desperdicia: cubre errores de corte durante la instalación y futuras extensiones de puntos.

**Cableado troncal:**

| Enlace | Longitud estimada (m) |
|---|---|
| MDF → SW-Recepcion | 7.5 |
| MDF → SW-RRHH | 2.5 |
| MDF → SW-Legal | 8.5 |
| MDF → SW-Capacitacion | 9.0 |
| MDF → SW-Diseno | 4.0 |
| MDF → SW-DirGeneral | 4.0 |
| MDF → SW-Backend | 9.0 |
| **Subtotal cobre** | **44.5** |
| MDF → SW-DataCenter (fibra, x2) | 40.0 |

Con holgura del 15% el cobre troncal suma unos 52 m, cubiertos con **1 bobina de Cat 6A**. La fibra se adquiere preterminada de fábrica en dos tramos de 30 m, ya que el terminado de fibra en campo requiere fusionadora y personal certificado.

---

## 6. Equipo activo y dimensionamiento

### 6.1 Función de cada elemento

| Elemento | Función en el flujo de conexión |
|---|---|
| **Switch core (48p + 4 SFP+)** | Concentra los ocho enlaces troncales y conmuta el tráfico entre departamentos. Los puertos SFP+ reciben la fibra del Data Center. |
| **Switch de acceso (16p)** | Punto de conexión de los hosts de cada departamento. Conmuta el tráfico local del segmento y lo entrega al core por su uplink. |
| **Switch Data Center (24p + 2 SFP)** | Conecta los tres servidores principales y termina los dos enlaces de fibra hacia el MDF. |
| **Patch panel** | Termina el cableado permanente. Todo cable de instalación muere en un panel; los movimientos se hacen con patch cords, de modo que el cable estructural no se manipula nunca. Esto es lo que da vida útil larga a la instalación. |
| **ODF** | Distribuidor de fibra. Termina y protege los pigtails del enlace al Data Center, permitiendo conectorización LC ordenada. |
| **Organizador de cables** | Guía los patch cords entre panel y switch, controlando el radio de curvatura y manteniendo el frente del rack accesible. |
| **UPS** | Sostiene el equipo activo durante cortes eléctricos y filtra variaciones de voltaje. |

### 6.2 Dimensionamiento

El edificio tiene **48 puntos de red**, por lo tanto el patch panel del MDF se dimensiona a **48 puertos** y el switch del MDF a **48 puertos**, cumpliendo la regla de que el switch tenga una cantidad de puertos igual o mayor a la del patch panel.

| Ubicación | Puntos servidos | Patch panel | Switch |
|---|---|---|---|
| MDF | 48 (total del edificio) | 48 puertos | 48 puertos + 4 SFP+ |
| Recepción | 4 | 12 puertos | 16 puertos |
| Recursos Humanos | 8 | 12 puertos | 16 puertos |
| Legal | 4 | 12 puertos | 16 puertos |
| Sala de Capacitación | 10 | 12 puertos | 16 puertos |
| Diseño e Innovación | 8 | 12 puertos | 16 puertos |
| Dirección General | 4 | 12 puertos | 16 puertos |
| Backend | 7 | 12 puertos | 16 puertos |
| Data Center | 3 | 12 puertos | 24 puertos + 2 SFP |

En cada gabinete departamental el switch de 16 puertos supera al patch panel de 12, dejando puertos libres para el uplink troncal y para crecimiento. En el MDF, de los 48 puertos del panel se ocupan inicialmente 8 con los enlaces troncales; los 40 restantes quedan como reserva y permiten, si en el futuro la empresa lo decide, migrar a un esquema de cableado directo desde cada puesto al MDF sin cambiar el panel.

---

## 7. Medio de transmisión del cableado troncal

Para los siete enlaces MDF → switch de departamento se seleccionó **UTP Cat 6A**, y para el enlace MDF → Data Center **fibra multimodo OM4 dúplex**.

Los criterios evaluados fueron tres:

**Velocidad de uplink.** El segmento más cargado es Sala de Capacitación con 10 hosts. Si todos transmitieran simultáneamente a 1 Gbps, el uplink necesitaría 10 Gbps para no ser cuello de botella. Cat 6A soporta 10GBASE-T, mientras que Cat 6 solo garantiza esa velocidad hasta 55 m y con restricciones de diafonía.

**Distancia.** Ningún enlace troncal de cobre supera los 18 m, muy lejos del límite de 100 m. La distancia no fue restrictiva; lo que decidió fue el ancho de banda.

**Costo y escalabilidad.** Cat 6A cuesta aproximadamente el doble que Cat 6 por metro, pero como los troncales suman apenas 45 m, el sobrecosto total es de unos pocos cientos de quetzales. Es una inversión mínima que evita recablear el backbone cuando la empresa migre sus puestos a 10 Gbps.

Para el Data Center la decisión fue distinta porque cambia el criterio dominante: ahí manda la disponibilidad. Se instalan dos enlaces de fibra OM4 por rutas independientes dentro de la escalerilla, de modo que un corte accidental de un cable no deja fuera de servicio a los tres servidores principales.

---

## 8. Canalización

| Segmento | Canalización | Justificación |
|---|---|---|
| Troncal, sobre el Pasillo Central | **Escalerilla metálica abierta** de 200 mm, suspendida sobre cielo falso | Recorre los 20 m del pasillo y alimenta los ocho departamentos. Al ser abierta permite agregar o retirar cables sin desmontar la ruta, disipa mejor el calor de haces densos, y facilita la inspección visual del tendido. El pasillo es zona de circulación, no de trabajo, así que la estética de una bandeja vista no es problema. |
| Horizontal, dentro de oficinas | **Canaleta plástica perimetral** de 32 x 12 mm, adosada a muro | Corre a lo largo de las paredes hasta cada toma. No requiere obra civil ni romper muros terminados, es de bajo costo, se instala rápido y protege el cable del contacto con mobiliario y personas. |
| Bajadas verticales a tomas | **Canaleta de derivación** o tubería EMT embutida donde exista muro seco | Baja el cable desde la canaleta perimetral hasta la altura de la caja de la toma, manteniendo el radio de curvatura mínimo. |
| Cruce del pasillo al Data Center | **Tubería EMT de 1"** | El tramo pasa por el vestíbulo, zona de acceso público. La tubería cerrada protege la fibra de manipulación y de golpes. |

Se descartó la escalerilla cerrada en el pasillo porque encarece la instalación y complica el mantenimiento, y en un edificio de oficinas de un nivel no hay condiciones ambientales que la justifiquen. Se descartó el piso técnico por el costo de obra sobre una edificación ya terminada.

---

## 9. Rack o gabinete del MDF

Se propone un **gabinete de piso cerrado de 24U, 600 x 800 mm, con puerta frontal de vidrio templado con llave y ventilación forzada**.

El razonamiento parte del equipo a alojar:

| U | Equipo |
|---|---|
| 1–2 | Patch panel 48 puertos Cat 6A |
| 3 | Organizador horizontal de cables |
| 4 | Switch core 48 puertos + 4 SFP+ |
| 5 | ODF de 12 puertos LC |
| 6 | Organizador horizontal de cables |
| 7–8 | UPS 1000 VA / 700 W |
| 9–24 | Reserva de crecimiento (16U libres) |

Se ocupan 8U de 24 disponibles, es decir un tercio. Las razones para elegir gabinete de piso y no de pared:

1. **Peso.** El UPS por sí solo pesa alrededor de 15 kg, y sumado al resto el conjunto supera lo razonable para un anclaje a muro tabique.
2. **Profundidad.** 800 mm permiten gestionar los patch cords por detrás del equipo y trabajar dentro del gabinete sin desmontar nada. Un gabinete de pared típico de 450 mm obliga a trabajar de frente y desordena el cableado.
3. **Crecimiento.** Los 16U libres absorben equipo futuro — un segundo switch, un firewall, un servidor de gestión — sin sustituir el gabinete.
4. **Seguridad y control ambiental.** Puerta con llave restringe el acceso físico, y la ventilación forzada mantiene la temperatura del switch core dentro de rango.

Para los ocho departamentos se propone **gabinete de pared de 6U**, ya que solo alojan un patch panel de 12 puertos (1U), un switch de 16 puertos (1U) y un UPS pequeño (2U). Ahí sí conviene el montaje en pared porque libera piso de oficina y el peso es bajo.

---

## 10. Consumo eléctrico y respaldo (UPS)

### 10.1 Estimación de consumo

| Equipo | Cantidad | Consumo unitario (W) | Total (W) |
|---|---|---|---|
| Switch core 48 puertos | 1 | 50 | 50 |
| Switch de acceso 16 puertos | 7 | 18 | 126 |
| Switch Data Center 24 puertos | 1 | 30 | 30 |
| **Total equipo activo de red** | | | **206 W** |

Los patch panels, el ODF y los organizadores son elementos pasivos y no consumen energía. La estimación no incluye servidores ni estaciones de trabajo, ya que el enunciado limita el cálculo al equipo activo de red.

### 10.2 Cálculo de la capacidad del UPS

```
Potencia real (W)          = 206 W
Factor de potencia típico  = 0.9
Potencia aparente (VA)     = 206 / 0.9 = 229 VA
Margen de crecimiento 30%  = 298 VA  →  ≈ 300 VA
```

### 10.3 Propuesta

El respaldo se distribuye en lugar de centralizarse, porque los switches de acceso están físicamente en sus departamentos y alimentarlos desde el MDF exigiría un circuito eléctrico dedicado a cada uno:

| Ubicación | UPS propuesto | Carga | Autonomía estimada |
|---|---|---|---|
| MDF | 1000 VA / 700 W, montaje en rack | 50 W (switch core) | Más de 60 min |
| Cada gabinete departamental | 500 VA / 300 W | 18 a 30 W | Más de 60 min |

El UPS del MDF se dimensiona por encima de la carga actual a propósito. La razón es que absorbe el equipo que ocupe los 16U de reserva sin necesidad de reemplazarlo, y trabajar al 7% de carga prolonga considerablemente la vida útil de las baterías.

---

## 11. Straight-through y crossover por enlace

| Enlace | Dispositivos en los extremos | Tipo de cable | Estándar aplicado | Justificación técnica |
|---|---|---|---|---|
| Toma de red → PC / laptop | Switch (DCE) ↔ Estación (DTE) | Straight-through | T568B / T568B | Dispositivos de distinto tipo. El switch ya invierte internamente TX y RX, por lo que el cable debe mantener el orden de pines idéntico en ambos extremos. |
| Toma de red → servidor | Switch ↔ Servidor (DTE) | Straight-through | T568B / T568B | Un servidor se comporta como estación desde el punto de vista de la NIC; aplica el mismo criterio. |
| Cableado horizontal: jack → patch panel departamental | Terminación pasiva | Straight-through | T568B / T568B | El cable permanente se poncha bajo el mismo estándar en ambos extremos. Cambiar de estándar en un extremo convertiría el enlace en crossover por error. |
| Patch panel departamental → switch de acceso | Panel pasivo ↔ Switch | Straight-through | T568B / T568B | El patch panel es transparente eléctricamente; el enlace sigue siendo switch ↔ host. |
| Troncal: SW-Recepcion ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Dispositivos del mismo tipo. Sin cruce, TX de un extremo quedaría enfrentado a TX del otro. |
| Troncal: SW-RRHH ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-Legal ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-Capacitacion ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-Diseno ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-DirGeneral ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-Backend ↔ SW-CORE | Switch ↔ Switch | **Crossover** | T568A / T568B | Igual criterio. |
| Troncal: SW-DataCenter ↔ SW-CORE | Switch ↔ Switch (fibra) | Cruce TX/RX | No aplica T568 | En fibra el cruce se resuelve con patch cord dúplex tipo A-B: el TX de un extremo se conecta al RX del otro. Los estándares T568A/B son exclusivos de par trenzado. |

**Aclaración importante.** En la práctica, todo el equipo Gigabit moderno implementa **Auto-MDI/MDIX**, que detecta la configuración del cable y cruza los pares electrónicamente. Con ese equipo, un cable straight-through funciona igual en un enlace switch-switch. La tabla documenta el criterio clásico del estándar porque es el que aplica cuando hay equipo antiguo o cuando la negociación automática está deshabilitada, y porque es lo que corresponde documentar en el diseño de Capa 1.

---

## 12. Disposición de pines

### 12.1 Cable straight-through (T568B en ambos extremos)

Es el que se usa en todo el cableado horizontal.

| Pin | Extremo A (T568B) | Extremo B (T568B) | Función en 1000BASE-T |
|---|---|---|---|
| 1 | Blanco/Naranja | Blanco/Naranja | BI_DA+ |
| 2 | Naranja | Naranja | BI_DA− |
| 3 | Blanco/Verde | Blanco/Verde | BI_DB+ |
| 4 | Azul | Azul | BI_DC+ |
| 5 | Blanco/Azul | Blanco/Azul | BI_DC− |
| 6 | Verde | Verde | BI_DB− |
| 7 | Blanco/Café | Blanco/Café | BI_DD+ |
| 8 | Café | Café | BI_DD− |

### 12.2 Cable crossover (T568A en un extremo, T568B en el otro)

Es el que corresponde a los enlaces troncales switch ↔ switch.

| Pin | Extremo A (T568A) | Extremo B (T568B) |
|---|---|---|
| 1 | Blanco/Verde | Blanco/Naranja |
| 2 | Verde | Naranja |
| 3 | Blanco/Naranja | Blanco/Verde |
| 4 | Azul | Azul |
| 5 | Blanco/Azul | Blanco/Azul |
| 6 | Naranja | Verde |
| 7 | Blanco/Café | Blanco/Café |
| 8 | Café | Café |

Se observa que el cruce ocurre en los pares 2 y 3 — pines 1, 2, 3 y 6 — que son los que transportan datos en 10BASE-T y 100BASE-TX. Los pines 4, 5, 7 y 8 conservan su posición en ambos estándares; en Gigabit Ethernet estos cuatro también transportan datos, razón por la cual un crossover Gigabit real debe cruzar además los pares azul y café.

### 12.3 Regla de ponchado adoptada

Todo el **cableado horizontal se poncha bajo T568B en ambos extremos**: en el jack RJ45 del faceplate y en el puerto correspondiente del patch panel departamental. Mezclar estándares dentro del cableado permanente es una de las causas más comunes de enlaces que negocian a baja velocidad o que simplemente no levantan, y el problema es difícil de diagnosticar porque el cable se ve correctamente instalado.

---

## 13. Etiquetado de cables

### 13.1 Formato adoptado

- **Cableado horizontal:** `[Departamento]-PR##`
- **Cableado troncal:** `MDF-[Departamento]`

### 13.2 Tabla de etiquetado

| Etiqueta | Tipo | Origen | Destino | Puertos |
|---|---|---|---|---|
| Recepcion-PR01 | Horizontal | SW-Recepcion | Toma doble, escritorio de atención | 2 |
| Recepcion-PR02 | Horizontal | SW-Recepcion | Toma doble, área de espera | 2 |
| RRHH-PR01 | Horizontal | SW-RRHH | Toma doble, mesa norte | 2 |
| RRHH-PR02 | Horizontal | SW-RRHH | Toma doble, mesa sur | 2 |
| RRHH-PR03 | Horizontal | SW-RRHH | Toma doble, muro sur | 2 |
| RRHH-PR04 | Horizontal | SW-RRHH | Toma doble, muro este | 2 |
| Legal-PR01 | Horizontal | SW-Legal | Toma doble, escritorio principal | 2 |
| Legal-PR02 | Horizontal | SW-Legal | Toma doble, área de trabajo | 2 |
| Capacitacion-PR01 | Horizontal | SW-Capacitacion | Toma cuádruple, mesa 1 | 4 |
| Capacitacion-PR02 | Horizontal | SW-Capacitacion | Toma cuádruple, mesa 2 | 4 |
| Capacitacion-PR03 | Horizontal | SW-Capacitacion | Toma doble, mesa 3 | 2 |
| Diseno-PR01 | Horizontal | SW-Diseno | Toma doble, estación UI/UX | 2 |
| Diseno-PR02 | Horizontal | SW-Diseno | Toma doble, Data Analytics | 2 |
| Diseno-PR03 | Horizontal | SW-Diseno | Toma doble, laboratorio QA | 2 |
| Diseno-PR04 | Horizontal | SW-Diseno | Toma doble, servidor del área | 2 |
| DirGeneral-PR01 | Horizontal | SW-DirGeneral | Toma doble, escritorio de dirección | 2 |
| DirGeneral-PR02 | Horizontal | SW-DirGeneral | Toma doble, mesa de reuniones | 2 |
| Backend-PR01 | Horizontal | SW-Backend | Toma doble, escritorios 1-2 | 2 |
| Backend-PR02 | Horizontal | SW-Backend | Toma doble, escritorios 3-4 | 2 |
| Backend-PR03 | Horizontal | SW-Backend | Toma doble, escritorios 5-6 | 2 |
| Backend-PR04 | Horizontal | SW-Backend | Toma unitaria, servidor del área | 1 |
| DataCenter-PR01 | Horizontal | SW-DataCenter | Toma triple, servidores principales | 3 |
| MDF-Recepcion | Troncal | SW-CORE | SW-Recepcion | 1 |
| MDF-RRHH | Troncal | SW-CORE | SW-RRHH | 1 |
| MDF-Legal | Troncal | SW-CORE | SW-Legal | 1 |
| MDF-Capacitacion | Troncal | SW-CORE | SW-Capacitacion | 1 |
| MDF-Diseno | Troncal | SW-CORE | SW-Diseno | 1 |
| MDF-DirGeneral | Troncal | SW-CORE | SW-DirGeneral | 1 |
| MDF-Backend | Troncal | SW-CORE | SW-Backend | 1 |
| MDF-DataCenter | Troncal (fibra) | SW-CORE | SW-DataCenter | 2 hilos x2 |

Cada cable se etiqueta en **ambos extremos**, con etiqueta autolaminada resistente para que no se borre con el manejo.

---

## 14. Comparación con el estándar TIA/EIA-606

El estándar **TIA/EIA-606** (Administración de Infraestructura de Telecomunicaciones) define cómo identificar, etiquetar y documentar todos los componentes de una instalación. El esquema usado en esta práctica cumple su espíritu pero es una versión reducida. Las diferencias concretas:

| Aspecto | Esquema de esta práctica | Lo que exige TIA/EIA-606 |
|---|---|---|
| **Alcance de la identificación** | Solo se identifican cables (horizontal y troncal). | Exige identificadores únicos para **cuatro clases de elementos**: espacios (cuartos de telecomunicaciones, salas de equipo), rutas de canalización (bandejas, tuberías, ductos), cables y sus terminaciones, y el sistema de puesta a tierra. En nuestro diseño, la escalerilla del pasillo y el propio MDF no llevan identificador. |
| **Codificación por colores** | No se usa. Todas las etiquetas son iguales. | Define un **código de colores obligatorio** por tipo de terminación: naranja para punto de demarcación con el proveedor, verde para conexión de red, azul para terminación horizontal, blanco para backbone principal, gris para backbone secundario, café para backbone entre edificios, amarillo para circuitos auxiliares y rojo para sistemas telefónicos. |
| **Registros y documentación** | Una tabla estática dentro de este manual. | Exige un **sistema de registros vinculados** que relacione cada identificador con su ubicación física, su ruta, sus terminaciones en ambos extremos y su historial de cambios. El registro debe actualizarse cada vez que se hace un movimiento, adición o cambio (MAC). |
| **Identificador de puerto** | Se identifica la toma completa (`Legal-PR01`), no cada puerto individual del faceplate. | Exige identificar cada terminación de manera única, de modo que un faceplate doble genera dos identificadores distintos, no uno. |

**Por qué en un entorno real se opta por el estándar completo.** En un data center o cuarto de telecomunicaciones en producción se manejan cientos o miles de enlaces, y el personal que instala rara vez es el mismo que da mantenimiento tres años después. Con un esquema simplificado como el de esta práctica, localizar el otro extremo de un cable exige seguirlo físicamente o probar puerto por puerto, lo cual en un rack lleno es lento y arriesgado — se desconecta un servicio activo por equivocación. El sistema de registros de la 606 permite responder "¿a dónde va este cable y qué servicio interrumpo si lo desconecto?" consultando la documentación, sin tocar nada. Eso reduce directamente el tiempo medio de reparación y evita caídas provocadas durante el mantenimiento.

---

## 15. Flujo de conexión end-to-end

Recorrido completo de una computadora del Departamento de Backend hasta el switch core:

```
[PC de escritorio - Backend]
        │  Patch cord Cat 6 de 1 m (straight-through, T568B)
        ▼
[Jack RJ45 - toma Backend-PR01, faceplate doble]
        │  Cable horizontal UTP Cat 6, ponchado T568B
        │  Ruta: canaleta perimetral 32x12 mm por muro de oficina
        ▼
[Patch panel 12p - gabinete de pared 6U de Backend]
        │  Patch cord Cat 6 de 0.5 m
        ▼
[Switch de acceso 16p - SW-Backend]
        │  Enlace troncal UTP Cat 6A, etiqueta MDF-Backend
        │  Ruta: canaleta → escalerilla metálica del Pasillo Central
        ▼
[Patch panel 48p Cat 6A - MDF]
        │  Patch cord Cat 6A de 1 m
        ▼
[Switch core 48p - SW-CORE, gabinete de piso 24U]
```

Para un servidor del Data Center el recorrido cambia en el último tramo:

```
[Servidor - Data Center]
        │  Patch cord Cat 6 de 1 m
        ▼
[Jack RJ45 - toma DataCenter-PR01, faceplate triple]
        │  Cable horizontal UTP Cat 6
        ▼
[Patch panel 12p → SW-DataCenter 24p + 2 SFP]
        │  Fibra multimodo OM4 dúplex, dos enlaces redundantes
        │  Ruta: tubería EMT 1" → escalerilla del Pasillo Central
        ▼
[ODF 12 puertos LC - MDF]
        │  Patch cord de fibra LC-LC dúplex
        ▼
[Puertos SFP+ del switch core]
```

El punto clave del diseño es que **el cable permanente nunca se manipula**. Todos los cambios de puesto o de puerto se hacen moviendo patch cords en el frente del patch panel, lo que preserva la certificación del cableado estructural durante toda su vida útil.

---

## 16. Presupuesto estimado

Precios de referencia del mercado guatemalteco, en quetzales. Deben confirmarse mediante cotización formal antes de la compra.

| # | Descripción | Cant. | P. unitario (Q) | Total (Q) |
|---|---|---|---|---|
| 1 | Switch core 48p gigabit administrable + 4 SFP+ | 1 | 9,500.00 | 9,500.00 |
| 2 | Switch de acceso 16p gigabit administrable | 7 | 2,200.00 | 15,400.00 |
| 3 | Switch Data Center 24p + 2 SFP | 1 | 4,500.00 | 4,500.00 |
| 4 | Transceptor SFP+ 10G multimodo SR | 4 | 1,200.00 | 4,800.00 |
| 5 | Patch panel 48 puertos Cat 6A, 2U | 1 | 1,100.00 | 1,100.00 |
| 6 | Patch panel 12 puertos Cat 6, 1U | 8 | 350.00 | 2,800.00 |
| 7 | ODF 12 puertos LC dúplex | 1 | 800.00 | 800.00 |
| 8 | Gabinete de piso 24U 600x800 con ventilación | 1 | 5,500.00 | 5,500.00 |
| 9 | Gabinete de pared 6U | 8 | 1,400.00 | 11,200.00 |
| 10 | UPS 1000 VA / 700 W montaje en rack | 1 | 3,200.00 | 3,200.00 |
| 11 | UPS 500 VA | 8 | 650.00 | 5,200.00 |
| 12 | Bobina UTP Cat 6 de 305 m | 3 | 1,300.00 | 3,900.00 |
| 13 | Bobina UTP Cat 6A de 305 m | 1 | 2,600.00 | 2,600.00 |
| 14 | Fibra OM4 dúplex LC-LC preterminada 30 m | 2 | 900.00 | 1,800.00 |
| 15 | Jack RJ45 Cat 6 keystone | 48 | 45.00 | 2,160.00 |
| 16 | Faceplate (1, 2, 3 y 4 puertos) | 22 | 35.00 | 770.00 |
| 17 | Caja sobrepuesta para toma | 22 | 25.00 | 550.00 |
| 18 | Escalerilla metálica 200 mm + accesorios | 22 m | 160.00 | 3,520.00 |
| 19 | Canaleta plástica 32x12 mm (tramo 2 m) | 50 | 40.00 | 2,000.00 |
| 20 | Patch cord Cat 6 (1 m y 2 m) | 60 | 35.00 | 2,100.00 |
| 21 | Organizador horizontal de cables 1U | 3 | 200.00 | 600.00 |
| 22 | Herramienta: ponchadora, impacto, tester | 1 | 1,500.00 | 1,500.00 |
| 23 | Mano de obra de instalación y certificación | 1 | 12,000.00 | 12,000.00 |
| | **Subtotal** | | | **97,500.00** |
| | **Imprevistos (5%)** | | | **4,875.00** |
| | **TOTAL ESTIMADO** | | | **102,375.00** |

---

## 17. Consideraciones de escalabilidad futura

- **Patch panel del MDF:** 40 de 48 puertos libres tras la instalación inicial.
- **Switches de acceso:** cada uno de 16 puertos atiende como máximo 10 hosts, dejando al menos 5 puertos libres además del uplink. El departamento con menos margen es Sala de Capacitación, que puede crecer 5 hosts más sin cambiar equipo.
- **Gabinete del MDF:** 16U libres de 24, suficientes para un firewall, un segundo switch y un servidor de gestión.
- **Cable sobrante:** aproximadamente 255 m de Cat 6 y 250 m de Cat 6A, que cubren la instalación de nuevos puntos sin comprar bobina.
- **Canalización:** la escalerilla abierta de 200 mm queda ocupada a menos de un tercio de su capacidad, permitiendo agregar troncales sin ampliar la ruta.
- **Fibra:** el ODF de 12 puertos tiene 8 libres, y el switch core conserva 2 puertos SFP+ disponibles.

## 18. Compra individual de materiales o contratación de proveedor

La recomendación es un **esquema mixto**:

**Compra individual** para todo el material de commodity — bobinas de UTP, canaleta, faceplates, jacks, patch cords, gabinetes de pared y UPS pequeños. Son productos estandarizados donde el margen del proveedor no agrega valor técnico y la diferencia de precio puede llegar al 25%.

**Contratación de proveedor especializado** para tres partidas concretas:

1. **Instalación y certificación del cableado horizontal.** Un proveedor certificado entrega los resultados de certificación por enlace con equipo Fluke, lo que respalda la garantía del fabricante — típicamente de 20 a 25 años en sistemas Panduit o Siemon. Sin esa certificación no hay garantía extendida.
2. **Enlaces de fibra.** El terminado en campo requiere fusionadora y personal certificado. Como el proyecto solo tiene dos enlaces, comprar el equipo o capacitar personal no se justifica.
3. **Instalación de la escalerilla metálica.** Involucra anclajes a losa y trabajo en altura, con implicaciones de seguridad ocupacional.

El criterio de fondo es que se contrata al tercero donde su intervención aporta garantía, certificación o seguridad, y se compra directo donde solo aportaría margen comercial.

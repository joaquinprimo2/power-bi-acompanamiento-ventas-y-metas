# Acompañamiento de Ventas y Metas - Dashboard en Power BI

Panel de acompañamiento de ventas con dos vistas: una operativa (facturación, ticket medio, tiempos de entrega) y otra de seguimiento de metas (real vs objetivo, desviación mensual), construido en Power BI con modelo de datos relacional y medidas DAX desde cero.

## Objetivo del análisis

No alcanza con vender, hay que saber si se está cumpliendo el objetivo y dónde. Este dashboard responde tres preguntas de negocio:

- ¿Cómo evolucionó la facturación y el volumen de pedidos mes a mes, por sucursal?
- ¿Qué sucursales están cumpliendo su meta de facturación y cuáles no?
- ¿Cómo varía el desvío entre lo real y lo proyectado a lo largo del año?

## Principal hallazgo

En 2023, la sucursal Matriz cerró un 9% por debajo de su meta de facturación, el único caso negativo de todo el dashboard. El resto de las sucursales (ES, MG, SP) superó su objetivo. Un buen dashboard no es el que muestra todo en verde, es el que también deja ver lo que no salió como se esperaba.

## Estructura del dashboard

| Sección | Qué muestra |
|---|---|
| Visión General de Ventas | Facturación, ticket medio, total de pedidos y entregas por tienda |
| Total de pedidos por hora | Distribución horaria de pedidos |
| Tiempo medio de entrega por tienda | Comparación de tiempos logísticos entre sucursales |
| Total de pedidos y entregas por mes | Evolución mensual del volumen operativo |
| Facturación y margen de lucro por categoría | Rentabilidad por categoría de producto |
| Acompañamiento de Metas | Facturación real vs meta, diferencia y % de cumplimiento por sucursal |
| Facturación por mes acumulado | Real vs meta en formato YTD |
| Desviación del objetivo por mes | Evolución mensual del % de desvío |

Filtros: Año (2022/2023) y Sucursal (Matriz, ES, MG, SP). El año recalcula toda la vista; la sucursal hace zoom en el gráfico de tendencia sin alterar la tabla comparativa, para poder mirar las 4 sucursales lado a lado sin perder ese contexto.

## Dataset

- Sucursales: Matriz, Sucursal ES, Sucursal MG, Sucursal SP
- Período: 2022-2023
- Tablas: FVentas y FMetas (hechos), dFuncionario, dProductos, dTienda, DCalendario (dimensiones), TablaFilter y Tabla_Medidas de apoyo

## Proceso técnico

### 1. Modelado de datos

Modelo relacional con dos tablas de hechos (FVentas, FMetas) conectadas a cuatro dimensiones (dFuncionario, dProductos, dTienda, DCalendario), más una TablaFilter y una Tabla_Medidas separada para organizar el DAX.

### 2. Medidas DAX

| Medida | Función principal | Qué calcula |
|---|---|---|
| Facturación | SUMX | Cantidad x valor unitario, sumado sobre FVentas |
| Facturación_LY | CALCULATE + SAMEPERIODLASTYEAR | Facturación del mismo período del año anterior |
| FacturacionYTD | CALCULATE + DATESYTD | Facturación acumulada en el año |
| Ganancia_LY | CALCULATE + SAMEPERIODLASTYEAR | Ganancia del mismo período del año anterior |
| Margen_de_ganancia% | DIVIDE | Ganancias / Facturación |
| Metas | SUM | Suma de Valor_Meta desde FMetas |
| MetaYTD | CALCULATE + DATESYTD | Meta acumulada en el año |
| Dif (Real-Meta) | Resta entre medidas | Facturación menos Metas |
| %Meta | DIVIDE | Dif (Real-Meta) / Metas |
| Ticket_Medio | AVERAGEX | Promedio de Cantidad x valor_unitario por transacción |
| Qtd_Pedidos | COUNTROWS | Número de pedidos en FVentas |
| Qtd_entregas | CALCULATE + USERELATIONSHIP | Pedidos filtrados por fecha de entrega (relación inactiva con DCalendario) |
| Tiempo_medio_entrega_hr | AVERAGEX + DATEDIFF | Promedio de horas entre fecha/hora de venta y fecha/hora de entrega |
| Facturación_TTienda | CALCULATE + ALL | Facturación total sin el filtro de sucursal, para comparar contra el total |
| Product_Dist | DISTINCTCOUNT | Cantidad de productos distintos vendidos |
| Total_Funcionaarios | COUNT | Cantidad de funcionarios registrados |

### 3. Diseño visual

Dos pantallas conectadas mediante un menú de navegación (Visión General de Ventas / Metas de Ventas), con paginación y segmentadores sincronizados de Año y Sucursal.

## Herramientas utilizadas

Power BI Desktop, Power Query, DAX

## Próximos pasos

Incorporar una vista de rentabilidad por producto cruzando FVentas con costos, hoy no explotada en esta versión.

Este proyecto forma parte de mi portfolio de análisis de datos.

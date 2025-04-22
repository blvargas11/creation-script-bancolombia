# Consultas SQL para Base de Datos Bancaria

## Enunciado 1: Clientes con múltiples cuentas y sus saldos totales

**Necesidad:** El banco necesita identificar a los clientes que tienen más de una cuenta, mostrar cuántas cuentas tiene cada uno y el saldo total acumulado en todas sus cuentas, ordenados por saldo total de mayor a menor.

**Consulta SQL:**
```sql

SELECT c.id_cliente, COUNT(c.num_cuenta) AS cantidad_cuentas, SUM(c.saldo) AS saldo_total
FROM Cuenta c
GROUP BY c.id_cliente
HAVING COUNT(c.num_cuenta) > 1
ORDER BY saldo_total DESC;

```

## Enunciado 2: Comparativa entre depósitos y retiros por cliente

**Necesidad:** El departamento de análisis financiero necesita comparar los montos totales de depósitos y retiros realizados por cada cliente, para identificar patrones de comportamiento financiero.

**Consulta SQL:**
```sql

SELECT t.num_cuenta,
       SUM(CASE WHEN t.tipo_transaccion = 'deposito' THEN t.monto ELSE 0 END) AS total_depositos,
       SUM(CASE WHEN t.tipo_transaccion = 'retiro' THEN t.monto ELSE 0 END) AS total_retiros
FROM Transaccion t
GROUP BY t.num_cuenta;

```

## Enunciado 3: Cuentas sin tarjetas asociadas

**Necesidad:** El departamento de tarjetas necesita identificar todas las cuentas que no tienen tarjetas asociadas para ofrecer productos específicos a estos clientes.

**Consulta SQL:**
```sql

SELECT c.num_cuenta, c.id_cliente, c.tipo_cuenta
FROM Cuenta c
LEFT JOIN Tarjeta t ON c.num_cuenta = t.num_cuenta
WHERE t.id_tarjeta IS NULL;

```

## Enunciado 4: Análisis de saldos promedio por tipo de cuenta y comportamiento transaccional

**Necesidad:** La gerencia necesita un análisis comparativo del saldo promedio entre cuentas de ahorro y corriente, pero solo considerando aquellas cuentas que han tenido al menos una transacción en los últimos 30 días.

**Consulta SQL:**
```sql

SELECT c.tipo_cuenta,
       AVG(c.saldo) AS saldo_promedio
FROM Cuenta c
JOIN Transaccion t ON c.num_cuenta = t.num_cuenta
WHERE t.fecha > CURRENT_DATE - INTERVAL '30 days'
GROUP BY c.tipo_cuenta;

```

## Enunciado 5: Clientes con transferencias pero sin retiros en cajeros

**Necesidad:** El equipo de marketing digital necesita identificar a los clientes que utilizan transferencias pero no realizan retiros por cajeros automáticos, para dirigir campañas de banca digital.

**Consulta SQL:**
```sql

SELECT DISTINCT c.id_cliente
FROM Cliente c
JOIN Cuenta cu ON c.id_cliente = cu.id_cliente
JOIN Transaccion t ON cu.num_cuenta = t.num_cuenta
WHERE t.tipo_transaccion = 'transferencia'
AND c.id_cliente NOT IN (
    SELECT DISTINCT c.id_cliente
    FROM Cliente c
    JOIN Cuenta cu ON c.id_cliente = cu.id_cliente
    JOIN Transaccion t ON cu.num_cuenta = t.num_cuenta
    WHERE t.tipo_transaccion = 'retiro' AND t.descripcion LIKE '%cajero%'
);


```

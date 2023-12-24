# RESOLUCION EN DAX 

## Nuevas columnas 
-- DayOfWeek = WEEKDAY('Call_Center_1999_DataSet'[date])
ALTER TABLE Call_Center_1999_DataSet
ADD COLUMN DayOfWeek INT;

UPDATE Call_Center_1999_DataSet
SET DayOfWeek = DAYOFWEEK(`date`);

-- QTimeMinutes = 'Call_Center_1999_DataSet'[q_time] / 60
ALTER TABLE Call_Center_1999_DataSet
ADD COLUMN QTimeMinutes DECIMAL(10,2);

UPDATE Call_Center_1999_DataSet
SET QTimeMinutes = `q_time` / 60;

-- MesDelAño = MONTH('Call_Center_1999_DataSet'[date])
ALTER TABLE Call_Center_1999_DataSet
ADD COLUMN MesDelAño INT;

UPDATE Call_Center_1999_DataSet
SET MesDelAño = MONTH(`date`);


## Nuevas tablas 

SELECT
    hora,
    SUM(CASE WHEN DayOfWeek = 2 THEN 1 ELSE 0 END) AS Lunes,
    SUM(CASE WHEN DayOfWeek = 3 THEN 1 ELSE 0 END) AS Martes,
    SUM(CASE WHEN DayOfWeek = 4 THEN 1 ELSE 0 END) AS Miércoles,
    SUM(CASE WHEN DayOfWeek = 5 THEN 1 ELSE 0 END) AS Jueves,
    SUM(CASE WHEN DayOfWeek = 6 THEN 1 ELSE 0 END) AS Viernes,
    SUM(CASE WHEN DayOfWeek = 7 THEN 1 ELSE 0 END) AS Sábado,
    SUM(CASE WHEN DayOfWeek = 1 THEN 1 ELSE 0 END) AS Domingo,
    SUM(CASE WHEN outcome = 'HANG' THEN 1 ELSE 0 END) AS 'HANG Count',
    SUM(CASE WHEN outcome = 'AGENT' THEN 1 ELSE 0 END) AS 'AGENT Count',
    SUM(CASE WHEN outcome = 'PHANTOM' THEN 1 ELSE 0 END) AS 'PHANTOM Count'
FROM
    Call_Center_1999_DataSet_modificado
GROUP BY
    hora;

SELECT
    "TOTALLLAMADAS",
    COUNT(DISTINCT call_id) AS TOTALLLAMADAS,
    COUNT(DISTINCT CASE WHEN vru_time IS NOT NULL THEN call_id END) AS ENTRANENVRU,
    COUNT(DISTINCT CASE WHEN vru_time IS NOT NULL AND q_start <> 0 AND ser_time <> 0 THEN call_id END) AS ENTRANENCOLA,
    COUNT(DISTINCT CASE WHEN outcome = 'AGENT' AND ser_time <> 0 THEN call_id END) AS RECIBENATENCIONPERSONALIZADA
FROM
    Call_Center_1999_DataSet;

WITH ResumenLlamadasQTimePorDiaCTE AS (
    SELECT
        DayOfWeek,
        PorcentajeLlamadasPorDia * 100 AS porcentajedellamadasenlasemana,
        PromedioQTimePorDia_ / 100 AS PromedioQTime,
        TipoDeOutcomePorDia AS tipodeoutcomepordia
    FROM
        Call_Center_1999_DataSet
)
SELECT *
FROM ResumenLlamadasQTimePorDiaCTE;

SELECT
    'Call_Center_1999_DataSet'[type],
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] = 2 THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype2,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] <> 2 THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype0y1
FROM
    Call_Center_1999_DataSet
GROUP BY
    'Call_Center_1999_DataSet'[type];

SELECT
    'Call_Center_1999_DataSet'[type],
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] = 2 AND 'Call_Center_1999_DataSet'[outcome] = 'AGENT' THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype2AGENT,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] <> 2 AND 'Call_Center_1999_DataSet'[outcome] = 'AGENT' THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype0y1AGENT,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] = 2 AND 'Call_Center_1999_DataSet'[outcome] <> 'AGENT' THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype2NOAGENT,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] <> 2 AND 'Call_Center_1999_DataSet'[outcome] <> 'AGENT' THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype0y1NOAGENT,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] = 2 THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype2,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[priority] <> 2 THEN 'Call_Center_1999_DataSet'[type] END) AS Calltype0y1
FROM
    Call_Center_1999_DataSet
GROUP BY
    'Call_Center_1999_DataSet'[type];

SELECT
    'Call_Center_1999_DataSet'[outcome],
    COUNT(DISTINCT CASE WHEN CAST('Call_Center_1999_DataSet'[priority] AS INT) = 2 THEN 'Call_Center_1999_DataSet'[outcome] END) AS Calltype2,
    COUNT(DISTINCT CASE WHEN CAST('Call_Center_1999_DataSet'[priority] AS INT) <> 2 THEN 'Call_Center_1999_DataSet'[outcome] END) AS Calltype0y1
FROM
    Call_Center_1999_DataSet
GROUP BY
    'Call_Center_1999_DataSet'[outcome];

SELECT
    COUNT(DISTINCT 'Call_Center_1999_DataSet'[call_id]) AS TOTALLLAMADAS,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[vru_time] IS NOT NULL THEN 'Call_Center_1999_DataSet'[call_id] END) AS ENTRANENVRU,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[vru_time] IS NOT NULL AND 'Call_Center_1999_DataSet'[q_start] <> 0 AND 'Call_Center_1999_DataSet'[ser_time] <> 0 THEN 'Call_Center_1999_DataSet'[call_id] END) AS ENTRANENCOLA,
    COUNT(DISTINCT CASE WHEN 'Call_Center_1999_DataSet'[outcome] = 'AGENT' AND 'Call_Center_1999_DataSet'[ser_time] <> 0 THEN 'Call_Center_1999_DataSet'[call_id] END) AS RECIBENATENCIONPERSONALIZADA
FROM
    Call_Center_1999_DataSet;

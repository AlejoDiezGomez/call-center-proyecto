# RESOLUCION EN DAX 

## Nuevas columnas 
* DayOfWeek = WEEKDAY('Call_Center_1999_DataSet'[date])
* QTimeMinutes = 'Call_Center_1999_DataSet'[q_time] / 60
* MesDelAño = MONTH('Call_Center_1999_DataSet'[date])

## Nuevas tablas 
 * horapordiadelasemana = 
  SUMMARIZE(
    'Call_Center_1999_DataSet_modificado',
    'Call_Center_1999_DataSet_modificado'[hora],
    
    "Lunes", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 2), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Martes", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 3), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Miércoles", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 4), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Jueves", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 5), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Viernes", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 6), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Sábado", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 7), 'Call_Center_1999_DataSet_modificado'[hora]),
    "Domingo", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[DayOfWeek] = 1), 'Call_Center_1999_DataSet_modificado'[hora]),

    "HANG Count", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[outcome] = "HANG"), 'Call_Center_1999_DataSet_modificado'[hora]),
    "AGENT Count", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[outcome] = "AGENT"), 'Call_Center_1999_DataSet_modificado'[hora]),
    "PHANTOM Count", COUNTAX(FILTER('Call_Center_1999_DataSet_modificado', 'Call_Center_1999_DataSet_modificado'[outcome] = "PHANTOM"), 'Call_Center_1999_DataSet_modificado'[hora])
)

    
 * llamadas por dia = 
   SUMMARIZE(
    'Call_Center_1999_DataSet',
    "TOTALLLAMADAS", COUNTAX(ALL('Call_Center_1999_DataSet'), 'Call_Center_1999_DataSet'[call_id]),
    "ENTRANENVRU", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[vru_time]), 1),
    "ENTRANENCOLA", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[vru_time] && 'Call_Center_1999_DataSet'[q_start] <> 0 && 'Call_Center_1999_DataSet'[ser_time] <> 0), 1),
    "RECIBENATENCIONPERSONALIZADA", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[outcome] = "AGENT" && 'Call_Center_1999_DataSet'[ser_time] <> 0), 1)
)

 * ResumenLlamadasQTimePorDia = 
   ADDCOLUMNS(
    VALUES('Call_Center_1999_DataSet'[DayOfWeek]),
    "porcentajedellamadasenlasemana", [PorcentajeLlamadasPorDia] * 100,
    "PromedioQTime", [PromedioQTimePorDia_]/100,
    "tipodeoutcomepordia", [TipoDeOutcomePorDia]
) 
 * tipoDeLlamadaPorCliente = 
SUMMARIZE(
    'Call_Center_1999_DataSet',
    'Call_Center_1999_DataSet'[type],
    "Calltype2", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[priority] = 2), 'Call_Center_1999_DataSet'[type]),
    "Calltype0y1", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[priority] <> 2), 'Call_Center_1999_DataSet'[priority])
)
 * tipoDeLlamadaPorClienteytipodeoutcome = 
SUMMARIZE(
    'Call_Center_1999_DataSet',
    'Call_Center_1999_DataSet'[type],
    "Calltype2AGENT", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] = 2 && 'Call_Center_1999_DataSet'[outcome] = "AGENT"
                )
            )
        ),
    "Calltype0y1AGENT", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] <> 2 && 'Call_Center_1999_DataSet'[outcome] = "AGENT"
                )
            )
        ),
        "Calltype2NOAGENT", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] = 2 && 'Call_Center_1999_DataSet'[outcome] <>"AGENT"
                )
            )
        ),
    "Calltype0y1NOAGENT", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] <> 2 && 'Call_Center_1999_DataSet'[outcome] <> "AGENT"
                )
            )
        ),
        "Calltype2", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] = 2 
                )
            )
        ),
    "Calltype0y1", 
        CALCULATE(
            COUNTROWS(
                FILTER(
                    'Call_Center_1999_DataSet', 
                    'Call_Center_1999_DataSet'[priority] <> 2 
                )
            )
        )
     
) 
 * tipoDeOUTCOME = 
SUMMARIZE(
    'Call_Center_1999_DataSet',
    'Call_Center_1999_DataSet'[outcome],
    "Calltype2", COUNTAX(FILTER('Call_Center_1999_DataSet', VALUE('Call_Center_1999_DataSet'[priority]) = 2 ), 'Call_Center_1999_DataSet'[outcome]),
    "Calltype0y1", COUNTAX(FILTER('Call_Center_1999_DataSet', VALUE('Call_Center_1999_DataSet'[priority]) <> 2), 'Call_Center_1999_DataSet'[outcome])
)

 *  volumenDeLlamadas = 
SUMMARIZE(
    'Call_Center_1999_DataSet',
    "TOTALLLAMADAS", COUNTAX(ALL('Call_Center_1999_DataSet'), 'Call_Center_1999_DataSet'[call_id]),
    "ENTRANENVRU", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[vru_time]), 1),
    "ENTRANENCOLA", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[vru_time] && 'Call_Center_1999_DataSet'[q_start] <> 0 && 'Call_Center_1999_DataSet'[ser_time] <> 0), 1),
    "RECIBENATENCIONPERSONALIZADA", COUNTAX(FILTER('Call_Center_1999_DataSet', 'Call_Center_1999_DataSet'[outcome] = "AGENT" && 'Call_Center_1999_DataSet'[ser_time] <> 0), 1)
) 

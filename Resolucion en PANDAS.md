# RESOLUCION EN PANDAS

## Nuevas columnas
### Agregamos las columnas DayOfWeek, QTimeMinutes y MesDelAño
df['DayOfWeek'] = df['date'].dt.weekday + 1  # Se suma 1 porque el índice de días de la semana en pandas comienza en 0
df['QTimeMinutes'] = df['q_time'] / 60
df['MesDelAño'] = df['date'].dt.month

### Agrupar por 'hora', 'DayOfWeek', 'MesDelAño', 'outcome' y contar las ocurrencias
grouped_df = df.groupby(['hora', 'DayOfWeek', 'MesDelAño', 'outcome']).size().reset_index(name='count')

### Crear una columna 'DayOfWeekName' para el nombre del día de la semana
day_mapping = {1: 'Domingo', 2: 'Lunes', 3: 'Martes', 4: 'Miércoles', 5: 'Jueves', 6: 'Viernes', 7: 'Sábado'}
grouped_df['DayOfWeekName'] = grouped_df['DayOfWeek'].map(day_mapping)

### Filtrar por cada día de la semana y contar las ocurrencias
result = pd.pivot_table(grouped_df, values='count', index=['hora', 'MesDelAño'], columns=['DayOfWeekName', 'outcome'], fill_value=0)

### Renombrar las columnas según el patrón original
result.columns = [f'{day}_{outcome}_Count' for day, outcome in result.columns]

### Agregar una columna adicional para el total de ocurrencias por hora y mes
result['Total_Count'] = result.sum(axis=1)

print(result)

## Nuevas tablas  

### Agregamos las columnas DayOfWeek, QTimeMinutes y MesDelAño
df['DayOfWeek'] = df['date'].dt.weekday + 1  # Se suma 1 porque el índice de días de la semana en pandas comienza en 0
df['QTimeMinutes'] = df['q_time'] / 60
df['MesDelAño'] = df['date'].dt.month

### Agrupar por 'hora', 'DayOfWeek', 'MesDelAño' y contar las ocurrencias
grouped_df = df.groupby(['hora', 'DayOfWeek', 'MesDelAño', 'outcome']).size().reset_index(name='count')

### Crear una columna 'DayOfWeekName' para el nombre del día de la semana
day_mapping = {1: 'Domingo', 2: 'Lunes', 3: 'Martes', 4: 'Miércoles', 5: 'Jueves', 6: 'Viernes', 7: 'Sábado'}
grouped_df['DayOfWeekName'] = grouped_df['DayOfWeek'].map(day_mapping)

### Filtrar por cada día de la semana y contar las ocurrencias
result = pd.pivot_table(grouped_df, values='count', index=['hora', 'MesDelAño'], columns=['DayOfWeekName', 'outcome'], fill_value=0)

### Renombrar las columnas según el patrón original
result.columns = [f'{day}_{outcome}_Count' for day, outcome in result.columns]

### Agregar una columna adicional para el total de ocurrencias por hora y mes
result['Total_Count'] = result.sum(axis=1)

### Calcular el total de llamadas
total_llamadas = len(df['call_id'].unique())

### Calcular el total de llamadas que entran en la VRU
entran_en_vru = len(df[df['vru_time'].notnull()])

### Calcular el total de llamadas que entran en cola
entran_en_cola = len(df[(df['vru_time'].notnull()) & (df['q_start'] != 0) & (df['ser_time'] != 0)])

### Calcular el total de llamadas que reciben atención personalizada
reciben_atencion_personalizada = len(df[(df['outcome'] == 'AGENT') & (df['ser_time'] != 0)])

### Crear un nuevo DataFrame con los resultados
result_df = pd.DataFrame({
    'TOTALLLAMADAS': [total_llamadas],
    'ENTRANENVRU': [entran_en_vru],
    'ENTRANENCOLA': [entran_en_cola],
    'RECIBENATENCIONPERSONALIZADA': [reciben_atencion_personalizada]
})

### Calcular porcentaje de llamadas por día
porcentaje_llamadas_por_dia = df.groupby('DayOfWeek')['PorcentajeLlamadasPorDia'].mean() * 100

### Calcular el promedio de QTime por día
promedio_qtime_por_dia = df.groupby('DayOfWeek')['PromedioQTimePorDia_'].mean() / 100

### Obtener el tipo de outcome por día (asumiendo que ya existe una columna llamada 'TipoDeOutcomePorDia' en el DataFrame)
tipo_de_outcome_por_dia = df.groupby('DayOfWeek')['TipoDeOutcomePorDia'].first()

### Crear un nuevo DataFrame con los resultados
result_df = pd.DataFrame({
    'porcentajedellamadasenlasemana': porcentaje_llamadas_por_dia,
    'PromedioQTime': promedio_qtime_por_dia,
    'tipodeoutcomepordia': tipo_de_outcome_por_dia
}).reset_index()

### Supongamos que 'df' es tu DataFrame de pandas

### Contar el número total de llamadas para cada tipo
call_type_counts = df.groupby('type').size().reset_index(name='Calltype2')

### Contar el número de llamadas con prioridad diferente de 2 para cada tipo
call_type_counts['Calltype0y1'] = df[df['priority'] != 2].groupby('type').size().rename('Calltype0y1')

### Llenar NaN con 0
call_type_counts = call_type_counts.fillna(0)

### Crear un DataFrame para cada condición
df_type_2_agent = df[(df['priority'] == 2) & (df['outcome'] == 'AGENT')]
df_type_0_1_agent = df[(df['priority'] != 2) & (df['outcome'] == 'AGENT')]
df_type_2_no_agent = df[(df['priority'] == 2) & (df['outcome'] != 'AGENT')]
df_type_0_1_no_agent = df[(df['priority'] != 2) & (df['outcome'] != 'AGENT')]
df_type_2 = df[df['priority'] == 2]
df_type_0_1 = df[df['priority'] != 2]

### Contar el número de filas para cada condición
call_type_counts = pd.DataFrame({
    'type': df['type'].unique(),
    'Calltype2AGENT': [len(df_type_2_agent[df_type_2_agent['type'] == t]) for t in df['type'].unique()],
    'Calltype0y1AGENT': [len(df_type_0_1_agent[df_type_0_1_agent['type'] == t]) for t in df['type'].unique()],
    'Calltype2NOAGENT': [len(df_type_2_no_agent[df_type_2_no_agent['type'] == t]) for t in df['type'].unique()],
    'Calltype0y1NOAGENT': [len(df_type_0_1_no_agent[df_type_0_1_no_agent['type'] == t]) for t in df['type'].unique()],
    'Calltype2': [len(df_type_2[df_type_2['type'] == t]) for t in df['type'].unique()],
    'Calltype0y1': [len(df_type_0_1[df_type_0_1['type'] == t]) for t in df['type'].unique()]
})

### Contar el número de llamadas para cada tipo de outcome
outcome_counts = df.groupby('outcome').size().reset_index(name='Calltype2')

### Contar el número de llamadas con prioridad diferente de 2 para cada tipo de outcome
outcome_counts['Calltype0y1'] = df[df['priority'] != 2].groupby('outcome').size().reset_index(name='Calltype0y1')['Calltype0y1']

### Llenar NaN con 0 en caso de que no haya llamadas con un tipo específico de outcome
outcome_counts = outcome_counts.fillna(0)

### Calcular el total de llamadas
total_llamadas = len(df['call_id'].unique())

### Calcular el total de llamadas que entran en la VRU
entran_en_vru = len(df[df['vru_time'].notnull()])

### Calcular el total de llamadas que entran en cola
entran_en_cola = len(df[(df['vru_time'].notnull()) & (df['q_start'] != 0) & (df['ser_time'] != 0)])

### Calcular el total de llamadas que reciben atención personalizada
reciben_atencion_personalizada = len(df[(df['outcome'] == 'AGENT') & (df['ser_time'] != 0)])

### Crear un nuevo DataFrame con los resultados
volumen_de_llamadas = pd.DataFrame({
    'TOTALLLAMADAS': [total_llamadas],
    'ENTRANENVRU': [entran_en_vru],
    'ENTRANENCOLA': [entran_en_cola],
    'RECIBENATENCIONPERSONALIZADA': [reciben_atencion_personalizada]
})





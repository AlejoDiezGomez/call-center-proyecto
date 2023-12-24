# Documentacion de la resolucion 
## Primer paso 
Lo primero fue identificar los errores en el DataSet. 
Se identifico que habia datos mal registrados en la columna call_id los cuales fuero remplazados por "0". Ademas se agrego una nueva columna llamada hora. 
Todo esto se realizo con python y pandas en la siguente linea de codigo 
import pandas as pd

### Especifica la ruta del archivo CSV
archivo_csv = 'Call_Center_1999_DataSet.csv'

### Lee el archivo CSV en un DataFrame de pandas
data_frame = pd.read_csv(archivo_csv, sep=';')

### Define una función para cambiar la cadena a "0" si no es completamente numérica
def cambiar_a_cero(valor):
    if isinstance(valor, str) and not valor.isdigit():
        return '0'
    else:
        return valor

### Aplica la función a la columna 'call_id'
data_frame['call_id'] = data_frame['call_id'].apply(cambiar_a_cero)

### Crea la nueva columna 'hora' tomando los dígitos antes del primer ":"
data_frame['hora'] = data_frame['vru_entry'].str.split(':', n=1).str[0]

### Especifica la ruta del nuevo archivo CSV
nuevo_archivo_csv = 'Call_Center_1999_DataSet_modificado.csv'

### Guarda el DataFrame modificado en el nuevo archivo CSV
data_frame.to_csv(nuevo_archivo_csv, index=False)

### Imprime un mensaje de confirmación
print(f"El DataFrame modificado se ha guardado en {nuevo_archivo_csv}")

### Imprime el DataFrame después de la modificación
print(data_framehh)

## Segundo paso 
Abri el archivo csv en power bi y segui resolviendolo en esta herramienta. Utilizando leguaje DAX por la comodidad que brinda la herramienta Power BI. De igual forma voy a documentar como seria resolverlo en pandas o en SQL

Agrugue nuevas columnas al DataSet original y nuevas tablas para la creacion de los graficos.

## RESOLUCION EN 

 * [DAX](/Resolucion%20en%20DAX.md)
 * [PANDAS](/Resolucion%20en%20PANDAS.md)
 * [SQL](/Resolucion%20en%20SQL.md)

